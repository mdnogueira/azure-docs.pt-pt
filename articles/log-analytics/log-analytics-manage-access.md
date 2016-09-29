<properties
    pageTitle="Gerir o acesso ao Log Analytics | Microsoft Azure"
    description="Efetue a gestão do acesso ao Log Analytics utilizando uma variedade de tarefas administrativas em utilizadores, contas, áreas de trabalho do OMS e contas do Azure."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/16/2016"
    ms.author="banders"/>


# Gerir o acesso ao Log Analytics

Para gerir o acesso ao Log Analytics, utilizará uma variedade de tarefas administrativas em utilizadores, contas, áreas de trabalho do OMS e contas do Azure. Para criar uma nova área de trabalho no Operations Management Suite (OMS), escolha um nome de área de trabalho, associe-o à sua conta e escolha uma localização geográfica. Uma área de trabalho é essencialmente um contentor que inclui informações da conta e informações de configuração simples para a conta. O utilizador ou outros membros da sua organização podem utilizar várias áreas de trabalho do OMS para gerir diferentes conjuntos de dados recolhidos de toda ou de partes da sua infraestrutura de TI.

O artigo [Introdução ao Log Analytics](log-analytics-get-started.md) mostra-lhe como começar rapidamente e o resto deste artigo descreve mais detalhadamente algumas das ações necessárias para gerir o acesso ao OMS.

Embora possa não ter de executar todas as tarefas de gestão inicialmente, abrangeremos nas secções seguintes todas as tarefas normais que pode executar:

- Determinar o número de áreas de trabalho necessárias
- Gerir contas e utilizadores
- Adicionar um grupo a uma área de trabalho existente
- Ligar uma área de trabalho existente a uma subscrição do Azure
- Atualizar uma área de trabalho para um plano de dados pago
- Alterar o tipo de plano de dados
- Adicionar uma Organização do Azure Active Directory a uma área de trabalho existente
- Fechar a área de trabalho do OMS

## Determinar o número de áreas de trabalho necessárias

Uma área de trabalho é um recurso do Azure e é um contentor onde os dados são recolhidos, agregados, analisados e apresentados no portal do OMS.

É possível criar várias áreas de trabalho do Log Analytics OMS e para os utilizadores terem acesso a uma ou mais áreas de trabalho. Em geral, pretenderá minimizar o número de áreas de trabalho, uma vez que isto lhe permitirá consultar e correlacionar a maior parte dos dados. Esta secção descreve quando pode ser útil criar mais do que uma área de trabalho.

Atualmente, uma área de trabalho do Log Analytics fornece:

- Uma localização geográfica para armazenamento de dados
- Granularidade para faturação
- Isolamento de dados

Com base nas características acima, pode pretender criar várias áreas de trabalho se:

- For uma empresa global e precisar de dados armazenados em regiões específicas por motivos de soberania ou conformidade dos dados.
- Estiver a utilizar o Azure e pretender evitar custos de transferência de dados de saída ao ter uma área de trabalho do Log Analytics na mesma região dos recursos do Azure geridos.
- Pretender atribuir custos a diferentes departamentos ou grupos empresariais com base na respetiva utilização. Quando cria uma área de trabalho para cada departamento ou grupo empresarial, a fatura e a declaração de utilização do Azure mostra os custos para cada área de trabalho em separado.
- For um fornecedor de serviços gerido e precisar de manter os dados de análise de registo de cada cliente isolados relativamente aos dos outros clientes.
- Gerir vários clientes e pretender que cada cliente, departamento ou grupo empresarial veja os seus próprios dados, mas não os dados de outros clientes, departamentos ou grupos empresariais.

Quando utilizar agentes para recolher dados, pode configurar cada agente para reportar à área de trabalho necessária.

Se estiver a utilizar o System Center Operations Manager, cada grupo de gestão do Operations Manager pode ser ligado apenas a uma área de trabalho. Pode instalar o Microsoft Monitoring Agent em computadores geridos pelo Operations Manager e fazer com que o agente reporte ao Operations Manager e a uma área de trabalho do Log Analytics diferente.

### Informações da área de trabalho

No portal do OMS, pode ver as informações da sua área de trabalho e escolher se pretende receber informações da Microsoft.

#### Ver informações da área de trabalho

1. No OMS, clique no mosaico **Definições**.
2. Clique no separador **Contas**.
3. Clique no separador **Informações da Área de Trabalho**.  
  ![Informações da Área de Trabalho](./media/log-analytics-manage-access/workspace-information.png)

## Gerir contas e utilizadores

Cada área de trabalho pode ter várias contas de utilizador associadas (conta Microsoft ou da Organização) pode ter acesso a várias áreas de trabalho do OMS.

Por predefinição, a conta Microsoft ou da Organização utilizada para criar a área de trabalho torna-se o Administrador da área de trabalho. Em seguida, o administrador pode convidar contas Microsoft adicionais ou selecionar os utilizadores a partir do Azure Active Directory.

O acesso à área de trabalho do OMS que é concedido às pessoas é controlado em 2 locais:

- No Azure, pode utilizar o controlo de acesso baseado em funções para fornecer acesso à subscrição do Azure e aos recursos do Azure associados. Isto também é utilizado no acesso ao PowerShell e à API REST.
- No portal do OMS, o acesso apenas ao portal do OMS e não à subscrição do Azure associada.

Se conceder às pessoas acesso ao portal do OMS, mas não à subscrição do Azure ao qual está ligado, os mosaicos das soluções Automatização, Cópia de Segurança e Site Recovery não mostram dados aos utilizadores quando estes iniciam sessão no portal do OMS.

Para permitir que todos os utilizadores vejam os dados nestas soluções, certifique-se de que têm, pelo menos, acesso de **leitor** à Conta de Automatização, Cofre de Cópias de Segurança e cofre de Site Recovery ligado à área de trabalho do OMS.   

### Gerir o acesso ao Log Analytics utilizando o portal do Azure

Se conceder às pessoas acesso à área de trabalho do Log Analytics utilizando as permissões do Azure, no portal do Azure, por exemplo, os mesmos utilizadores podem aceder ao portal do Log Analytics. Se os utilizadores estiverem no portal do Azure, podem navegar para o portal do OMS clicando na tarefa **Portal do OMS** quando visualizarem o recurso da área de trabalho do Log Analytics.

Alguns pontos a ter em conta sobre o portal do Azure:

- Isto não é o *Controlo de Acesso Baseado em Funções*. Se tiver permissões de acesso de *Leitor* no portal do Azure para a área de trabalho do Log Analytics, pode efetuar alterações através do portal do OMS. O portal do OMS tem um conceito de Administrador, Contribuinte e Utilizador com Acesso Só de Leitura. Se a conta na qual tem sessão iniciada estiver no Azure Active Directory associada à área de trabalho, será um Administrador no portal do OMS, caso contrário, será um Contribuinte.

- Quando iniciar sessão no portal do OMS através do http://mms.microsoft.com, por predefinição, vê a lista **Selecionar uma área de trabalho**. Contém apenas as áreas de trabalho adicionadas através do portal do OMS. Para ver as áreas de trabalho às quais tem acesso com subscrições do Azure, tem de especificar um inquilino como parte do URL. Por exemplo:

  `mms.microsoft.com/?tenant=contoso.com` O identificador do inquilino é, muitas vezes, a última parte do endereço de e-mail utilizado para iniciar sessão.

- Se a conta utilizada para iniciar sessão for uma conta no Azure Active Directory inquilino, que é normalmente o caso, exceto se o fizer como um CSP, será um *Administrador* no portal do OMS. Se a sua conta não estiver no Azure Active Directory inquilino, será um *Utilizador* no portal do OMS.

- Se pretender navegar diretamente para um portal ao qual tem acesso utilizando as permissões do Azure, tem de especificar o recurso como parte do URL. É possível obter este URL através do PowerShell.

  Por exemplo, `(Get-AzureRmOperationalInsightsWorkspace).PortalUrl`.

  O URL terá o seguinte aspeto:
`https://eus.mms.microsoft.com/?tenant=contoso.com&resource=%2fsubscriptions%2faaa5159e-dcf6-890a-a702-2d2fee51c102%2fresourcegroups%2fdb-resgroup%2fproviders%2fmicrosoft.operationalinsights%2fworkspaces%2fmydemo12`


### Gestão de utilizadores no portal do OMS

Gere utilizadores e grupos no separador **Gerir Utilizadores** no separador **Contas** da página Definições. Aí, pode efetuar as tarefas nas secções seguintes.  

![gerir utilizadores](./media/log-analytics-manage-access/setup-workspace-manage-users.png)

#### Adicionar um utilizador a uma área de trabalho existente

Siga os passos seguintes para adicionar um utilizador ou grupo a uma área de trabalho do OMS. O utilizador ou grupo poderá ver e agir sobre todos os alertas associados a esta área de trabalho.

>[AZURE.NOTE] Se pretender adicionar um utilizador ou grupo a partir da sua conta da organização do Azure Active Directory, primeiro tem de se certificar de que associou a conta do OMS ao domínio do Active Directory. Veja [Adicionar uma Organização do Azure Active Directory a uma área de trabalho existente](#add-an-azure-active-directory-organization-to-an-existing-workspace).

1. No OMS, clique no mosaico **Definições**.
2. Clique no separador **Contas** e, em seguida, clique no separador **Gerir Utilizadores**.
3. Na secção **Gerir Utilizadores**, escolha o tipo de conta a adicionar: **Conta da Organização**, **Conta Microsoft**, **Suporte da Microsoft**.
    - Se escolher Conta Microsoft, escreva o endereço de e-mail do utilizador associado à Conta Microsoft.
    - Se escolher Conta da Organização, pode introduzir parte do nome ou alias de e-mail do utilizador ou grupo e será apresentada uma lista de utilizadores e grupos. Selecione um utilizador ou grupo.
    - Utilize o Suporte da Microsoft para conceder a um engenheiro do Suporte da Microsoft acesso temporário à sua área de trabalho para ajudar na resolução de problemas.

    >[AZURE.NOTE] Para obter melhores resultados de desempenho, limite o número de grupos do Active Directory associados a uma única conta do OMS a três: um para os administradores, um para os contribuintes e um para os utilizadores só de leitura. A utilização de mais grupos pode afetar o desempenho do Log Analytics.

5. Escolha o tipo de utilizador ou grupo a adicionar: **Administrador**, **Contribuinte** ou **Utilizador com Acesso Só de Leitura**.  
6. Clique em **Adicionar**.

  Se estiver a adicionar uma conta Microsoft, é enviado para o e-mail fornecido um convite para aderir à área de trabalho. Depois de o utilizador seguir as instruções no convite para aderir ao OMS, pode ver os alertas e as informações desta conta do OMS e será possível ver as informações de utilizador no separador **Contas** da página **Definições**.
  Se estiver a adicionar uma conta da organização, o utilizador poderá aceder imediatamente ao Log Analytics.  
  ![e-mail de convite](./media/log-analytics-manage-access/setup-workspace-invitation-email.png)

#### Editar um tipo de utilizador existente

Pode alterar a função de conta para um utilizador associado à conta do OMS. Existem as seguintes opções de função:

 - *Administrador*: pode gerir utilizadores, ver e agir sobre todos os alertas e adicionar e remover servidores

 - *Contribuinte*: pode ver e agir sobre todos os alertas e adicionar e remover servidores

 - *Utilizador com Acesso Só de Leitura*: os utilizadores marcados como só de leitura não poderão:
   1. Adicionar/remover soluções. A galeria de soluções está oculta.
   2. Adicionar/modificar/remover mosaicos em **O Meu Dashboard**.
   3. Ver as páginas **Definições**. As páginas estão ocultas.
   4. Na vista Pesquisa, as tarefas Configuração do Power BI, Pesquisas Guardadas e Alertas estão ocultas.


#### Para editar uma conta

1. No OMS, clique no mosaico **Definições**.
2. Clique no separador **Contas** e, em seguida, clique no separador **Gerir Utilizadores**.
3. Selecione a função do utilizador que pretende alterar.
2. Na caixa de diálogo de confirmação, clique em **Sim**.

### Remover um utilizador de uma área de trabalho do OMS

Utilize os passos seguintes para remover um utilizador de uma área de trabalho do OMS. Tenha em atenção que isto não fecha a área de trabalho do utilizador. Em vez disso, remove a associação entre esse utilizador e a área de trabalho. Se um utilizador estiver associado a várias áreas de trabalho, este ainda poderá iniciar sessão no OMS e ver as outras áreas de trabalho.

1. No OMS, clique no mosaico **Definições**.
2. Clique no separador **Contas** e, em seguida, clique no separador **Gerir Utilizadores**.
3. Clique em **Remover** junto ao nome do utilizador que pretende remover.
4. Na caixa de diálogo de confirmação, clique em **Sim**.


### Adicionar um grupo a uma área de trabalho existente

1.  Siga os passos 1 a 4 em "Adicionar um utilizador a uma área de trabalho existente", acima.
2.  Em **Escolher Utilizador/Grupo**, selecione **Grupo**.
    ![adicionar um grupo a uma área de trabalho existente](./media/log-analytics-manage-access/add-group.png)
3.  Introduza o Nome a Apresentar ou o endereço de E-mail para o grupo que pretende adicionar.
4.  Selecione o grupo nos resultados da lista e, em seguida, clique em **Adicionar**.

## Ligar uma área de trabalho existente a uma subscrição do Azure

É possível criar uma área de trabalho a partir do site [microsoft.com/oms](https://microsoft.com/oms).  No entanto, existem determinados limites para estas áreas de trabalho, sendo o mais relevante o limite de 500 MB/dia de carregamentos de dados se estiver a utilizar uma conta gratuita. Para efetuar alterações a esta área de trabalho, terá de *ligar a sua área de trabalho existente a uma subscrição do Azure*.

>[AZURE.IMPORTANT] Para ligar uma área de trabalho, a sua conta do Azure tem de ter acesso à área de trabalho que pretende ligar.  Por outras palavras, a conta que utiliza para aceder ao portal do Azure tem de ser **a mesma** conta utilizada para aceder à área de trabalho do OMS. Se não for este o caso, veja [Adicionar um utilizador a uma área de trabalho existente](#add-a-user-to-an-existing-workspace).

### Para ligar uma área de trabalho a uma subscrição do Azure no portal do OMS

Para ligar uma área de trabalho a uma subscrição do Azure no portal do OMS, o utilizador com sessão iniciada tem de ter uma conta do Azure paga. A área de trabalho que está a utilizar ativamente fica ligada à conta do Azure.

1. No OMS, clique no mosaico **Definições**.
2. Clique no separador **Contas** e, em seguida, clique no separador **Subscrição e Plano de Dados do Azure**.
3. Clique no plano de dados que pretende utilizar.
4. Clique em **Guardar**.  
  ![subscrição e planos de dados](./media/log-analytics-manage-access/subscription-tab.png)

O novo plano de dados é apresentado no friso do portal do OMS na parte superior da página Web.

![Friso do OMS](./media/log-analytics-manage-access/data-plan-changed.png)

### Para ligar uma área de trabalho a uma subscrição do Azure no portal do Azure

1.  Inicie sessão no [Portal do Azure](http://portal.azure.com).
2.  Procure o **Log Analytics (OMS)** e selecione-o.
3.  Verá a lista de áreas de trabalho existentes. Clique em **Adicionar**.  
    ![lista de áreas de trabalho](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4.  Em **Área de Trabalho do OMS**, clique em **Ou ligação existente**.  
    ![ligação existente](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5.  Clique em **Configurar definições necessárias**.  
    ![configurar definições necessárias](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6.  Verá a lista de áreas de trabalho que ainda não estão ligadas à sua conta do Azure. Selecione uma área de trabalho.  
    ![selecionar áreas de trabalho](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7.  Se for necessário, pode alterar os valores dos seguintes itens:
    - Subscrição
    - Grupo de recursos
    - Localização
    - Escalão de preço  
        ![alterar valores](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8.  Clique em **Criar**. A área de trabalho está agora ligada à sua conta do Azure.

>[AZURE.NOTE] Se não visualizar a área de trabalho que pretende ligar, a sua subscrição do Azure não tem acesso à área de trabalho do OMS que criou utilizando o site do OMS.  Terá de conceder acesso a esta conta a partir da área de trabalho do OMS utilizando o site do OMS. Para tal, veja [Adicionar um utilizador a uma área de trabalho existente](#add-a-user-to-an-existing-workspace).



## Atualizar uma área de trabalho para um plano de dados pago

Existem três tipos de planos de dados de área de trabalho para o OMS: **Gratuito**, **Standard** e **Premium**.  Se tiver um plano *Gratuito*, pode ter atingido o limite de dados de 500 MB.  Terá de atualizar a sua área de trabalho para um ***plano pay as you go*** para recolher dados que ultrapassam este limite. Pode converter o tipo de plano em qualquer altura.  Para obter mais informações sobre os preços do OMS, veja [Detalhes dos Preços](https://www.microsoft.com/en-us/server-cloud/operations-management-suite/pricing.aspx).

>[AZURE.IMPORTANT] Os planos de área de trabalho apenas podem ser alterados se estiverem *ligados* a uma subscrição do Azure.  Se tiver criado a sua área de trabalho no Azure ou *já* tiver ligado a área de trabalho, pode ignorar esta mensagem.  Se tiver criado a sua área de trabalho com o [site do OMS](http://www.microsoft.com/oms), terá de seguir os passos em [Ligar uma área de trabalho existente a uma subscrição do Azure](#link-an-existing-workspace-to-an-azure-subscription).

### Utilizar elegibilidades a partir do Suplemento OMS para o System Center

O Suplemento OMS para o System Center fornece elegibilidade para o plano Premium do Log Analytics OMS, descrito em [Preços do OMS](https://www.microsoft.com/en-us/server-cloud/operations-management-suite/pricing.aspx).

Quando comprar o suplemento OMS para o System Center, este é adicionado como uma elegibilidade no seu contrato do System Center. Qualquer subscrição do Azure criada ao abrigo deste contrato pode utilizar a elegibilidade. Isto permite-lhe, por exemplo, ter várias áreas de trabalho do OMS que utilizam a elegibilidade a partir do suplemento OMS.

Para garantir que a utilização de uma área de trabalho do OMS é aplicada às elegibilidades a partir do suplemento OMS, terá de:

1. Ligar a sua área de trabalho do OMS a uma subscrição do Azure que faça parte do Contrato Enterprise que inclui a compra do suplemento OMS e a utilização de uma subscrição do Azure
2. Selecionar o plano Premium para a área de trabalho

Ao rever a sua utilização no portal do Azure ou do OMS, não verá as elegibilidades do suplemento OMS. No entanto, pode ver as elegibilidades no Portal da Empresa.  

Se precisar de alterar a subscrição do Azure à qual está ligada a área de trabalho do OMS, pode utilizar o cmdlet [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) do Azure PowerShell.

### Utilizar o Azure Commitment a partir de um Contrato Enterprise

Se optar por utilizar preços autónomos para componentes do OMS, pagará por cada componente do OMS em separado e a utilização aparecerá na sua fatura do Azure.

Se tiver uma alocação monetária do Azure na inscrição empresarial à qual as subscrições do Azure estão ligadas, qualquer utilização do Log Analytics será debitada automaticamente na alocação monetária restante.

Se precisar de alterar a subscrição do Azure à qual está ligada a área de trabalho do OMS, pode utilizar o cmdlet [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) do Azure PowerShell.  



### Para alterar uma área de trabalho para um plano de dados pago

1.  Inicie sessão no [Portal do Azure](http://portal.azure.com).
2.  Procure o **Log Analytics (OMS)** e selecione-o.
3.  Verá a lista de áreas de trabalho existentes. Selecione uma área de trabalho.  
    ![lista de áreas de trabalho](./media/log-analytics-manage-access/manage-access-change-plan01.png)
4.  Em **Definições**, clique em **Escalão de preço**.  
    ![escalão de preço](./media/log-analytics-manage-access/manage-access-change-plan02.png)
5.  Em **Escalão de preço**, selecione um plano de dados e, em seguida, clique em **Selecionar**.  
    ![selecionar plano](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6.  Quando atualizar a vista no portal do Azure, verá **Escalão de preço** atualizado com o plano que selecionou.  
    ![atualizar escalão de preço](./media/log-analytics-manage-access/manage-access-change-plan04.png)

Agora pode recolher dados para além do limite de dados "gratuito".


## Adicionar uma Organização do Azure Active Directory a uma área de trabalho existente

Pode associar a área de trabalho do Log Analytics (OMS) a um domínio do Azure Active Directory. Isto permite-lhe adicionar utilizadores do Active Directory diretamente à sua área de trabalho do OMS sem necessidade de uma conta Microsoft separada.

Ao criar a área de trabalho a partir do portal do Azure ou ao ligar a sua área de trabalho a uma subscrição do Azure, o Azure Active Directory será ligado como a sua conta da organização.

Ao criar a área de trabalho a partir do portal do OMS, ser-lhe-á pedido para ligar a uma subscrição do Azure e a uma conta da organização.

### Para adicionar uma Organização do Azure Active Directory a uma área de trabalho existente

1. Na página Definições no OMS, clique em **Contas** e, em seguida, clique no separador **Informações da Área de Trabalho**.  
2. Reveja as informações sobre contas de organização e, em seguida, clique em **Adicionar Organização**.  
    ![adicionar organização](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Introduza as informações de identidade do administrador do seu domínio do Azure Active Directory. Posteriormente, verá uma confirmação a indicar que a área de trabalho está ligada ao seu domínio do Azure Active Directory.
    ![confirmação da área de trabalho ligada](./media/log-analytics-manage-access/manage-access-add-adorg02.png)

>[AZURE.NOTE] Uma vez ligada a sua conta a uma Conta da Organização, a ligação não pode ser removida nem alterada.

## Fechar a área de trabalho do OMS

Ao fechar uma área de trabalho do OMS, todos os dados relacionados com a área de trabalho são eliminados do serviço OMS dentro de 30 dias após o fecho da área de trabalho.

Se for um administrador e existirem vários utilizadores associados à área de trabalho, a associação entre esses utilizadores e a área de trabalho é interrompida. Se os utilizadores estiverem associados a outras áreas de trabalho, podem continuar a utilizar o OMS com essas áreas de trabalho. No entanto, se não estiverem associados a outras áreas de trabalho, terão de criar uma nova área de trabalho para utilizar o OMS.

### Para fechar uma área de trabalho do OMS

1. No OMS, clique no mosaico **Definições**.
2. Clique no separador **Contas** e, em seguida, clique no separador **Informações da Área de Trabalho**.
3. Clique em **Fechar Área de Trabalho**.
4. Selecione uma das razões para fechar a área de trabalho ou introduza uma razão diferente na caixa de texto.
5. Clique em **Fechar área de trabalho**.

## Passos seguintes

- Veja [Ligar computadores Windows ao Log Analytics](log-analytics-windows-agents.md) para adicionar agentes e recolher dados.
- [Adicionar soluções do Log Analytics a partir da Galeria de Soluções](log-analytics-add-solutions.md) para adicionar funcionalidade e recolher dados.
- [Configure definições de proxy e firewall no Log Analytics](log-analytics-proxy-firewall.md) se a organização utilizar um servidor proxy ou firewall para que os agentes possam comunicar com o serviço Log Analytics.



<!--HONumber=Sep16_HO3-->



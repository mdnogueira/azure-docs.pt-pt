<properties
    pageTitle="O meu primeiro runbook gráfico da Automatização do Azure | Microsoft Azure"
    description="Este tutorial orienta-o ao longo da criação, do teste e da publicação de um runbook gráfico simples."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
    keywords="runbook, runbook template, runbook automation, azure runbook"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/31/2016"
    ms.author="magoedte;bwren"/>

# O meu primeiro runbook gráfico

> [AZURE.SELECTOR] - [Gráfico](automation-first-runbook-graphical.md) - [PowerShell](automation-first-runbook-textual-PowerShell.md) - [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)

Este tutorial explica como criar um [ runbook gráfico](automation-runbook-types.md#graphical-runbooks) na Automatização do Azure.  Iremos começar com um runbook simples que vamos testar e publicar enquanto explicamos como controlar o estado da tarefa de runbook.  Em seguida, iremos modificar o runbook para gerir recursos do Azure, neste caso, iniciar uma máquina virtual do Azure.  Por último, iremos tornar o runbook mais robusto ao adicionar parâmetros do runbook e ligações condicionais.

## Pré-requisitos

Para concluir este tutorial, irá precisar do seguinte.

-   Subscrição do Azure.  Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou <a href="/pricing/free-account/" target="_blank">[inscrever-se numa conta gratuita](https://azure.microsoft.com/free/).
-   [Conta Run As do Azure](automation-sec-configure-azure-runas-account.md) para manter o runbook e autenticar-se nos recursos do Azure.  Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
-   Uma máquina virtual do Azure.  Iremos parar e iniciar esta máquina, pelo que não deve ser de produção.


## Passo 1 – criar um novo runbook

Vamos começar por criar um runbook simples que produz o texto *Olá, Mundo*.

1.  No Portal do Azure, abra a sua conta da Automatização.  
    A página da conta da Automatização dá-lhe uma vista rápida dos recursos nesta conta.  Já deverá ter alguns Recursos.  A maioria destes são os módulos que estão incluídos automaticamente na nova conta da Automatização.  Também deverá ter o recurso da Credencial que está mencionado nos [Pré-requisitos](#prerequisites).
2.  Clique no mosaico **Runbooks** para abrir a lista de runbooks.<br> ![Controlo de runbooks](media/automation-first-runbook-graphical/runbooks-control.png)
3.  Crie um runbook novo ao clicar no botão **Adicionar um runbook** e, em seguida, **Criar um runbook novo**.
4.  Atribua ao runbook o nome *MyFirstRunbook-Graphical*.
5.  Neste caso, vamos criar um [runbook gráfico](automation-graphical-authoring-intro.md), pelo que deve selecionar **Gráfico** para o **Tipo de runbook**.<br> ![Novo runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6.  Clique em **Criar** para criar o runbook e abrir o editor gráfico.

## Passo 2 – adicionar atividades ao runbook

O controlo da Biblioteca no lado esquerdo do editor permite-lhe selecionar as atividades a adicionar ao runbook.  Vamos adicionar um cmdlet **Write-Output** para apresentar texto a partir do runbook.

1.  No controlo da Biblioteca, clique na caixa de texto de pesquisa e escreva **Write-Output**.  Os resultados da pesquisa serão apresentados abaixo. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
2.  Desloque-se para baixo até à parte inferior da lista.  Pode clicar com o botão direito do rato em **Write-Output** e selecionar **Adicionar à tela** ou clicar na elipse junto ao cmdlet e, em seguida, selecionar **Adicionar à tela**.
3.  Clique na atividade **Write-Output** na tela.  Esta ação abre o painel de controlo de Configuração que lhe permite configurar a atividade.
4.  O campo **Etiqueta** utiliza o nome do cmdlet por predefinição, mas é possível alterá-lo para algo mais amigável. Altere-o para *Escrever Olá, Mundo para a saída*.
5.  Clique em **Parâmetros** para fornecer valores para os parâmetros do cmdlet.  
    Alguns cmdlets têm vários conjuntos de parâmetros e tem de selecionar o que irá utilizar. Neste caso, **Write-Output** tem apenas um conjunto de parâmetros, pelo que não precisa de selecionar um. <br> ![Propriedades de Write-Output](media/automation-first-runbook-graphical/write-output-properties-b.png)
6.  Selecione o parâmetro **InputObject**.  Este é o parâmetro onde irá especificar o texto a enviar para o fluxo de saída.
7.  Na lista pendente **Origem de dados**, selecione **Expressão do PowerShell**.  A lista pendente **Origem de dados** fornece diferentes origens que utiliza para preencher um valor do parâmetro.  
    Pode utilizar a saída de origens como outra atividade, um recurso de Automatização ou uma expressão do PowerShell.  Neste caso, pretendemos apenas apresentar o texto *Olá, Mundo*. Podemos utilizar uma expressão do PowerShell e especificar uma cadeia.
8.  Na caixa **Expressão**, escreva *“Olá, Mundo”* e, em seguida, clique duas vezes em **OK** para voltar à tela.<br> ![Expressão do PowerShell](media/automation-first-runbook-graphical/expression-hello-world.png)
9.  Guarde o runbook ao clicar em **Guardar**.<br> ![Guardar o runbook](media/automation-first-runbook-graphical/runbook-toolbar-save-revised20165.png)

## Passo 3 – testar o runbook

Antes de publicarmos o runbook para o disponibilizar na produção, queremos testá-lo para garantir que funciona corretamente.  Quando testa um runbook, executa a versão de **Rascunho** e vê o resultado de forma interativa.

1.  Clique em **Painel de teste** para abrir o painel de Teste.<br> ![Painel de teste](media/automation-first-runbook-graphical/runbook-toolbar-test-revised20165.png)
2.  Clique em **Iniciar** para iniciar o teste.  Esta deve ser a única opção ativada.
3.  Uma [tarefa do runbook](automation-runbook-execution.md) é criada e o respetivo estado é apresentado no painel.  
    O estado da tarefa começará como *Em fila* com a indicação de que está à espera que uma função de trabalho de runbook na nuvem fique disponível.  Em seguida, irá mudar para *A iniciar* quando uma função de trabalho reivindicar a tarefa e, em seguida, *A executar* quando o runbook começar a ser executado.  
4.  Quando a tarefa de runbook tiver concluído, o resultado é apresentado. No nosso caso, deveremos ver *Olá, Mundo*.<br> ![Olá, Mundo](media/automation-first-runbook-graphical/runbook-test-results.png)
5.  Feche o painel de Teste para voltar à tela.

## Passo 4 – publicar e iniciar o runbook

O runbook que criamos ainda está no modo de Rascunho. Precisamos de o publicar antes de o executarmos na produção.  Quando publica um runbook, substitui a versão Publicada existente pela versão de Rascunho.  No nosso caso, ainda não temos uma versão Publicada porque acabamos de criar o runbook.

1.  Clique em **Publicar** para publicar o runbook e, em seguida, em **Sim** quando lhe for pedido.<br> ![Publicar](media/automation-first-runbook-graphical/runbook-toolbar-publish-revised20166.png)
2.  Se se deslocar para a esquerda para ver o runbook no painel **Runbooks**, esta operação irá mostrar o **Estado da Criação de Conteúdos** de **Publicado**.
3.  Desloque-se para a direita para ver o painel de **MyFirstRunbook**.  
    As opções na parte superior permitem-nos iniciar o runbook, agendar o seu início num momento futuro ou criar um [webhook](automation-webhooks.md) para que possa ser iniciado através de uma chamada HTTP.
4.  Queremos apenas iniciar o runbook, pelo que deve clicar em **Iniciar** e, em seguida, em **Sim** quando lhe for pedido.<br> ![Iniciar o runbook](media/automation-first-runbook-graphical/runbook-controls-start-revised20165.png)
5.  É aberto um painel de tarefas para a tarefa de runbook que acabamos de criar.  Podemos fechar este painel, mas neste caso, deixá-lo-emos aberto para vermos o progresso da tarefa.
6.  O estado da tarefa é mostrado no **Resumo da Tarefa** e corresponde aos estados que vimos quando testamos o runbook.<br> ![Resumo da Tarefa](media/automation-first-runbook-graphical/runbook-job-summary.png)
7.  Quando o estado de runbook mostrar *Concluído*, clique em **Resultado**. O painel **Resultado** é aberto e é possível ver *Olá, Mundo* no mesmo.<br> ![Resumo da Tarefa](media/automation-first-runbook-graphical/runbook-job-output.png)  
8.  Feche o painel Resultado.
9.  Clique em **Todos os Registos** para abrir o painel Fluxos da tarefa de runbook.  Apenas devemos conseguir ver *Olá, Mundo* no fluxo de saída, mas isto pode mostrar outros fluxos de uma tarefa de runbook, tais como Verboso e Erro se o runbook escrever nos mesmos.<br> ![Resumo da Tarefa](media/automation-first-runbook-graphical/runbook-job-AllLogs.png)
10. Feche o painel Todos os Registos e o painel Tarefas para voltar ao painel de MyFirstRunbook.
11. Clique em **Tarefas** para abrir o painel Tarefas para este runbook.  Isto apresenta uma lista de todas as tarefas criadas por este runbook. Apenas devemos conseguir ver uma tarefa listada, uma vez que apenas executamos a tarefa uma vez.<br> ![Tarefas](media/automation-first-runbook-graphical/runbook-control-jobs.png)
12. Pode clicar nesta tarefa para abrir o mesmo painel de Tarefas que visualizamos quando iniciamos o runbook.  Isto permite-lhe voltar atrás no tempo e ver os detalhes de qualquer tarefa que foi criada para um determinado runbook.

## Passo 5 – criar recursos de variável

Testamos e publicamos o nosso runbook, mas, até ao momento, não faz nada de útil. Queremos que efetue a gestão dos recursos do Azure.  Antes de configurarmos o runbook para efetuar a autenticação, iremos criar uma variável para conter o ID de subscrição e referenciá-lo depois de configurarmos a atividade para efetuar a autenticação no passo 6 abaixo.  A inclusão de uma referência ao contexto de subscrição permite-lhe trabalhar facilmente entre várias subscrições.  Antes de continuar, copie o seu ID de subscrição da opção Subscrições do Painel de navegação.  

1. No painel Contas da Automatização, clique no mosaico **Recursos** e o painel **Recursos** é aberto.
2. No painel Recursos, clique no mosaico **Variáveis**.
3. No painel Variáveis, clique em **Adicionar uma variável**.<br>![Variável de Automatização](media/automation-first-runbook-graphical/create-new-subscriptionid-variable.png)
4. No painel Nova variável, na caixa **Nome**, introduza **AzureSubscriptionId** e na caixa **Valor** introduza o ID de subscrição.  Mantenha *cadeia* para o **Tipo** e o valor predefinido para **Encriptação**.  
5. Clique em **Criar** para criar a variável.  


## Passo 6 – adicionar autenticação para gerir os recursos do Azure

Agora que temos uma variável para conter o ID de subscrição, podemos configurar o nosso runbook para se autenticar com as credenciais Run As que são mencionadas nos [Pré-requisitos](#prerequisites).  Para o fazer, adicionamos o **Recurso** da ligação Run As do Azure e o cmdlet **Add-AzureRMAccount** à tela.  

1.  Abra o editor gráfico clicando em **Editar** no painel MyFirstRunbook.<br> ![Editar o runbook](media/automation-first-runbook-graphical/runbook-controls-edit-revised20165.png)
2.  Já não precisamos de **Escrever Olá, Mundo para a saída**, por isso clique com o botão direito do rato no mesmo e selecione **Eliminar**.
3.  No controlo da Biblioteca, expanda **Ligações** e adicione **AzureRunAsConnection** à tela ao selecionar **Adicionar à tela**.
4.  Na tela, selecione **AzureRunAsConnection** e, no painel de controlo de Configuração, escreva **Obter Ligação Run As** na caixa de texto **Etiqueta**.  É esta a ligação. 
5.  No controlo da Biblioteca, escreva **Add-AzureRmAccount** na caixa de texto de pesquisa.
6.  Adicione **Add-AzureRmAccount** à tela.<br> ![Add-AzureRMAccount](media/automation-first-runbook-graphical/search-powershell-cmdlet-addazurermaccount.png)
7.  Coloque o cursor sobre **Obter Ligação Run As** até aparecer um círculo na parte inferior da forma. Clique no círculo e arraste a seta para **Add-AzureRmAccount**.  A seta que acabou de criar é uma *ligação*.  O runbook será iniciado com **Obter Ligação Run As** e, em seguida, irá executar **Add-AzureRmAccount**.<br> ![Criar ligação entre atividades](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
8.  Na tela, selecione **Add-AzureRmAccount** e, no painel de controlo de Configuração, escreva **Iniciar sessão no Azure** na caixa de texto **Etiqueta**.
9.  Clique em **Parâmetros** e é apresentado o painel Configuração de Parâmetro de Atividade. 
10.  **Add-AzureRmAccount** tem vários conjuntos de parâmetros, pelo que temos de selecionar um antes de podermos fornecer valores de parâmetros.  Clique em **Conjunto de Parâmetros** e, em seguida, selecione o conjunto de parâmetros **ServicePrincipalCertificate**. 
11.  Depois de selecionar o conjunto de parâmetros, os parâmetros são apresentados no painel Configuração de Parâmetro de Atividade.  Clique em **APPLICATIONID**.<br> ![Adicionar parâmetros de conta do Azure RM](media/automation-first-runbook-graphical/add-azurermaccount-parameterset.png)
12.  No painel Valor do Parâmetro, selecione **Saída da atividade** para a **Origem de dados** e selecione **Obter Ligação Run As** na lista. Na caixa de texto **Caminho do campo**, escreva **ApplicationId** e, em seguida, clique em **OK**.  Estamos a especificar o nome da propriedade para o Caminho do campo porque a atividade produz um objeto com várias propriedades.
13.  Clique em **CERTIFICATETHUMBPRINT** e, no painel Valor do Parâmetro, selecione **Saída da atividade** para a **Origem de dados**.  Selecione **Obter Ligação Run As** na lista, na caixa de texto **Caminho do campo** escreva **CertificateThumbrprint** e, em seguida, clique em **OK**. 
14.  Clique em **SERVICEPRINCIPAL** e, no painel Valor do Parâmetro, selecione **ConstantValue** para a **Origem de dados**, clique na opção **True** e, em seguida, clique em **OK**.
15.  Clique em **TENANTID** e, no painel Valor do Parâmetro, selecione **Saída da atividade** para a **Origem de dados**.  Selecione **Obter Ligação Run As** na lista, na caixa de texto **Caminho do campo** escreva **TenantId** e, em seguida, clique duas vezes em **OK**.  
16.  No controlo da Biblioteca, escreva **Set-AzureRmContext** na caixa de texto de pesquisa.
17.  Adicione **Set-AzureRmContext** à tela.
18.  Na tela, selecione **Set-AzureRmContext** e, no painel de controlo de Configuração, escreva **Especificar Id de Subscrição** na caixa de texto **Etiqueta**.
19.  Clique em **Parâmetros** e é apresentado o painel Configuração de Parâmetro de Atividade. 
20. **Set-AzureRmContext** tem vários conjuntos de parâmetros, pelo que temos de selecionar um antes de podermos fornecer valores de parâmetros.  Clique em **Conjunto de Parâmetros** e, em seguida, selecione o conjunto de parâmetros **SubscriptionId**.  
21.  Depois de selecionar o conjunto de parâmetros, os parâmetros são apresentados no painel Configuração de Parâmetro de Atividade.  Clique em **SubscriptionID**
22.  No painel Valor do Parâmetro, selecione **Recurso de Variável** para a **Origem de dados**, selecione **AzureSubscriptionId** na lista e, em seguida, clique duas vezes em **OK**.   
23.  Coloque o cursor sobre **Iniciar sessão no Azure** até aparecer um círculo na parte inferior da forma. Clique no círculo e arraste a seta para **Especificar Id de Subscrição**.


O runbook deve ter o seguinte aspeto neste momento: <br>![Configuração de autenticação de runbook](media/automation-first-runbook-graphical/runbook-auth-config.png)

## Passo 7 – adicionar atividade para iniciar uma máquina virtual

Iremos agora adicionar uma atividade **Start-AzureRmVM** para iniciar uma máquina virtual.  Pode escolher qualquer máquina virtual na sua subscrição do Azure e, por agora, iremos codificar esse nome para o cmdlet.

1. No controlo da Biblioteca, escreva **Start-AzureRm** na caixa de texto de pesquisa.
2. Adicione **Start-AzureRmVM** à tela e, em seguida, clique e arraste-a para baixo de **Especificar Id de Subscrição**.
3. Coloque o cursor sobre **Especificar Id de Subscrição** até aparecer um círculo na parte inferior da forma.  Clique no círculo e arraste a seta para **Start-AzureRmVM**. 
4.  Selecione **Start-AzureRmVM**.  Clique em **Parâmetros** e, em seguida, em **Conjunto de Parâmetros** para ver os conjuntos de **Start-AzureRmVM**.  Selecione o conjunto de parâmetros **ResourceGroupNameParameterSetName**. Tenha em atenção que existem pontos de exclamação junto a **ResourceGroupName** e **Name**.  Isto indica que são parâmetros obrigatórios.  Tenha também em atenção que ambos esperam valores de cadeia.
5.  Selecione **Name**.  Selecione **Expressão do PowerShell** para a **Origem de dados** e escreva o nome da máquina virtual entre aspas duplas com a qual iniciaremos este runbook.  Clique em **OK**.<br>![Valor do Parâmetro Name de Start-AzureRmVM](media/automation-first-runbook-graphical/runbook-startvm-nameparameter.png)
6.  Selecione **ResourceGroupName**. Utilize **Expressão do PowerShell** para a **Origem de dados** e escreva o nome do grupo de recursos entre aspas duplas.  Clique em **OK**.<br> ![Parâmetros de Start-AzureRmVM](media/automation-first-runbook-graphical/startazurermvm-params.png)
8.  Clique em Painel de teste para que possamos testar o runbook.
9.  Clique em **Iniciar** para iniciar o teste.  Depois de terminar, verifique se a máquina virtual foi iniciada.

O runbook deve ter o seguinte aspeto neste momento: <br>![Configuração de autenticação de runbook](media/automation-first-runbook-graphical/runbook-startvm.png)

## Passo 8 – adicionar parâmetros de entrada adicionais ao runbook

O nosso runbook inicia atualmente a máquina virtual do grupo de recursos que especificamos no cmdlet **Start-AzureRmVM**, mas seria mais útil se pudéssemos especificar também quando o runbook é iniciado.  Iremos agora adicionar parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. Abra o editor gráfico clicando em **Editar** no painel **MyFirstRunbook**.
2. Clique em **Entrada e saída** e, em seguida, em **Adicionar entrada** para abrir o painel Parâmetro de Entrada do Runbook.<br> ![Entrada e saída do runbook](media/automation-first-runbook-graphical/runbook-toolbar-InputandOutput-revised20165.png)
3. Especifique *VMName* para o **Nome**.  Mantenha *cadeia* para o **Tipo**, mas pode alterar **Obrigatório** para *Sim*.  Clique em **OK**.
4. Crie um segundo parâmetro de entrada obrigatório denominado *ResourceGroupName* e, em seguida, clique em **OK** para fechar o painel **Entrada e saída**.<br> ![Parâmetros de entrada do runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
5. Selecione a atividade **Start-AzureRmVM** e, em seguida, clique em **Parâmetros**.
6. Altere a **Origem de dados** de **Nome** para **Entrada do runbook** e, em seguida, selecione **VMName**.<br>
7. Altere a **Origem de dados** de **ResourceGroupName** para **Entrada do runbook** e, em seguida, selecione **ResourceGroupName**.<br> ![Parâmetros de Start-AzureVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
8. Guarde o runbook e abra o painel de Teste.  Tenha em atenção que agora pode fornecer valores para as duas variáveis de entrada que serão utilizadas no teste.
9. Feche o painel de Teste.
10. Clique em **Publicar** para publicar a nova versão do runbook.
11. Pare a máquina virtual que iniciou no passo anterior.
12. Clique em **Iniciar** para iniciar o runbook.  Escreva **VMName** e **ResourceGroupName** na máquina virtual que vai iniciar.<br> ![Iniciar o Runbook](media/automation-first-runbook-graphical/runbook-start-inputparams.png)
13. Depois de o runbook terminar, verifique se a máquina virtual foi iniciada.

## Passo 9 – criar uma ligação condicional

Iremos agora modificar o runbook para que apenas tente iniciar a máquina virtual se esta ainda não tiver iniciado.  Para tal, iremos adicionar um cmdlet **Get-AzureRmVM** ao runbook que irá obter o estado de nível de instância da máquina virtual. Em seguida, iremos adicionar um módulo de código de Fluxo de Trabalho do PowerShell chamado **Obter Estado** com um fragmento de código do PowerShell para determinar se o estado da máquina virtual está em execução ou parado.  Uma ligação condicional do módulo **Obter Estado** apenas irá executar **Start-AzureRmVM** se o estado de execução atual estiver parado.  Por último, iremos gerar uma mensagem a informar se a VM foi iniciada com êxito ou não utilizando o cmdlet Write-Output do PowerShell.

1. Abra **MyFirstRunbook** no editor gráfico.
2. Remova a ligação entre **Especificar Id de Subscrição** e **Start-AzureRmVM** ao clicar na mesma e, em seguida, premir a tecla *Eliminar*.
3. No controlo da Biblioteca, escreva **Get-AzureRm** na caixa de texto de pesquisa.
4. Adicione **Get-AzureRmVM** à tela.
5. Selecione **Get-AzureRmVM** e, em seguida, **Conjunto de Parâmetros** para ver os conjuntos de **Get-AzureRmVM**.  Selecione o conjunto de parâmetros **GetVirtualMachineInResourceGroupNameParamSet**.  Tenha em atenção que existem pontos de exclamação junto a **ResourceGroupName** e **Name**.  Isto indica que são parâmetros obrigatórios.  Tenha também em atenção que ambos esperam valores de cadeia.
6. Em **Origem de dados** para **Nome**, selecione **Entrada do runbook** e, em seguida, selecione **VMName**.  Clique em **OK**.
7. Em **Origem de dados** para **ResourceGroupName**, selecione **Entrada do runbook** e, em seguida, selecione **ResourceGroupName**.  Clique em **OK**.
8. Em **Origem de dados** para **Estado**, selecione **Valor constante** e, em seguida, clique em **True**.  Clique em **OK**.  
9. Crie uma ligação de **Especificar Id de Subscrição** para **Get-AzureRmVM**.
10. No controlo da biblioteca, expanda **Controlo do Runbook** e adicione **Código** à tela.  
11. Crie uma ligação de **Get-AzureRmVM** para **Código**.  
12. Clique em **Código** e, no painel de Configuração, altere a etiqueta para **Obter Estado**.
13. Selecione o parâmetro **Código** e é apresentado o painel **Editor de Código**.  
14. No editor de código, cole o fragmento de código seguinte:

     ```
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText 
     $Statuses = ConvertFrom-Json $StatusesJson 
     $StatusOut ="" 
     foreach ($Status in $Statuses){ 
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"} 
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"} 
     } 
     $StatusOut 
     ```

15. Crie uma ligação de **Obter Estado** para **Start-AzureRmVM**.<br> ![Runbook com Módulo de Código](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
16. Selecione a ligação e, no painel de Configuração, altere **Aplicar condição** para **Sim**.   Tenha em atenção que a ligação muda para uma linha tracejada que indica que a atividade de destino apenas será executada se a condição for resolvida como verdadeira.  
17. Para **Expressão de condição**, escreva *$ActivityOutput['Get Status'] -eq "Stopped"*.  Agora, **Start-AzureRmVM** só será executado se a máquina virtual estiver parada.
18. No controlo da Biblioteca, expanda **Cmdlets** e, em seguida, **Microsoft.PowerShell.Utility**.
19. Adicione **Write-Output** à tela duas vezes.<br> ![Runbook com Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
20. No primeiro controlo **Write-Output**, altere o valor de **Etiqueta** para *Notify VM Started*.
21. Para **InputObject**, altere **Origem de dados** para **Expressão do PowerShell** e escreva a expressão *"$VMName successfully started."*.
22. No segundo controlo **Write-Output**, altere o valor de **Etiqueta** para *Notify VM Start Failed*
23. Para **InputObject**, altere **Origem de dados** para **Expressão do PowerShell** e escreva a expressão *"$VMName could not start."*.
24. Crie uma ligação de **Start-AzureRmVM** para **Notify VM Started** e **Notify VM Start Failed**.
25. Selecione a ligação para **Notify VM Started** e altere **Aplicar condição** para **True**.
26. Para a **Expressão de condição**, escreva *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*.  Agora, este controlo Write-Output apenas será executado se a máquina virtual for iniciada com êxito.
27. Selecione a ligação para **Notify VM Start Failed** e altere **Aplicar condição** para **True**.
28. Para a **Expressão de condição**, escreva *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*.  Agora, este controlo Write-Output apenas será executado se a máquina virtual não for iniciada com êxito. 
29. Guarde o runbook e abra o painel de Teste.
30. Inicie o runbook com a máquina virtual parada e este deve iniciar.

## Passos Seguintes

-   Para obter mais informações sobre a Criação de Gráficos, consulte [Criação de gráficos na Automatização do Azure](automation-graphical-authoring-intro.md)
-   Para começar com runbooks do PowerShell, consulte [O meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md)
-   Para começar com runbooks do fluxo de trabalho do PowerShell, consulte [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)



<!--HONumber=Jun16_HO2-->



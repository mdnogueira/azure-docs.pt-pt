---
title: "O meu primeiro runbook do PowerShell na Automatização do Azure | Microsoft Docs"
description: "Este tutorial orienta-o ao longo da criação, do teste e da publicação de um runbook do PowerShell simples."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: 
keywords: "Azure powershell, tutorial de script do powershell, automatização de powershell"
ms.assetid: a43b395a-e740-41a3-ae62-40eac9d0ec00
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/31/2017
ms.author: magoedte;sngun
ms.openlocfilehash: 2645d149df1f337700fb627aaff99e869fc8b8e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="my-first-powershell-runbook"></a>O meu primeiro runbook do PowerShell

> [!div class="op_single_selector"]
> * [Gráficos](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [python](automation-first-runbook-textual-python2.md)
> 

Este tutorial explica como criar um [ runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) na Automatização do Azure. Começamos com um runbook simples que testamos e publicamos enquanto explicamos como controlar o estado da tarefa de runbook. Em seguida, modificamos o runbook para gerir recursos do Azure, neste caso, iniciar uma máquina virtual do Azure. Por último, tornamos o runbook mais robusto ao adicionar parâmetros do runbook.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa do seguinte:

* Subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](automation-offering-get-started.md) para manter o runbook e autenticar-se nos recursos do Azure.  Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Paramos e iniciamos esta máquina, pelo que não deve ser uma VM de produção.

## <a name="step-1---create-new-runbook"></a>Passo 1 – criar um novo runbook
Vamos começar por criar um runbook simples que produz o texto *Olá, Mundo*.

1. No portal do Azure, abra a sua conta da Automatização.  
   A página da conta da Automatização dá-lhe uma vista rápida dos recursos nesta conta. Já deverá ter alguns recursos. A maioria destes são os módulos que estão incluídos automaticamente na nova conta da Automatização. Também deverá ter o recurso da Credencial que está mencionado nos [Pré-requisitos](#prerequisites).
2. Clique no mosaico **Runbooks** para abrir a lista de runbooks.<br><br> ![RunbooksControl](media/automation-first-runbook-textual-powershell/runbooks-control-tiles.png)  
3. Crie um runbook novo ao clicar no botão **Adicionar um runbook** e, em seguida, **Criar um runbook novo**.
4. Atribua ao runbook o nome *MyFirstRunbook PowerShell*.
5. Neste caso, vamos criar um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks), pelo que deve selecionar **Powershell** para o **tipo de Runbook**.<br><br> ![Tipo de Runbook](media/automation-first-runbook-textual-powershell/automation-runbook-type.png)  
6. Clique em **Criar** para criar o runbook e abra o editor de texto.

## <a name="step-2---add-code-to-the-runbook"></a>Passo 2 - adicionar código ao runbook
Pode escrever o código do tipo diretamente no runbook ou pode selecionar os cmdlets, runbooks e recursos no controlo da Biblioteca e adicioná-los ao runbook com quaisquer parâmetros relacionados. Nestas instruções, escrevemos diretamente no runbook.

1. Atualmente, o nosso runbook está vazio; escreva *Write-Output "Hello World."* no corpo do script.<br><br> ![Olá Mundo](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2. Guarde o runbook ao clicar em **Guardar**.<br><br> ![Botão Guardar](media/automation-first-runbook-textual-powershell/automation-runbook-edit-controls-save.png)  

## <a name="step-3---test-the-runbook"></a>Passo 3 – testar o runbook
Antes de publicarmos o runbook para o disponibilizar na produção, queremos testá-lo para garantir que funciona corretamente. Quando testa um runbook, executa a versão de **Rascunho** e veja o resultado de forma interativa.

1. Clique em **Painel de teste** para abrir o Painel de teste.<br><br> ![Painel de Teste](media/automation-first-runbook-textual-powershell/automation-runbook-edit-controls-test.png)  
2. Clique em **Iniciar** para iniciar o teste. Esta deve ser a única opção ativada.
3. Uma [tarefa do runbook](automation-runbook-execution.md) é criada e o respetivo estado é apresentado.  
   O estado da tarefa começa como *Em fila* com a indicação de que está à espera que uma função de trabalho de runbook na cloud fique disponível. Em seguida, irá mudar para *A iniciar* quando uma função de trabalho reivindicar a tarefa e, em seguida, *A executar* quando o runbook começar a ser executado.  
4. Quando a tarefa de runbook tiver concluído, o resultado é apresentado. No nosso caso, deveremos ver *Olá, Mundo*.<br><br> ![Resultado do Painel de Teste](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
5. Feche o painel de Teste para voltar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Passo 4 – publicar e iniciar o runbook
O runbook que criamos ainda está no modo de Rascunho. Precisamos de o publicar antes de o executarmos na produção.  Quando publica um runbook, substitui a versão Publicada existente pela versão de Rascunho.  No nosso caso, ainda não temos uma versão Publicada porque acabamos de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e, em seguida, em **Sim** quando lhe for pedido.<br><br> ![Botão Publicar](media/automation-first-runbook-textual-powershell/automation-runbook-edit-controls-publish.png)  
2. Se se deslocar para a esquerda para ver o runbook no painel **Runbooks** agora, esta operação irá mostrar o **Estado da Criação de Conteúdos** de **Publicado**.
3. Desloque-se para trás para a direita para ver o painel de **MyFirstRunbook-PowerShell**.  
   As opções na parte superior permitem-nos iniciar o runbook, ver o runbook, agendar o seu início num momento futuro ou criar um [webhook](automation-webhooks.md) para que possa ser iniciado através de uma chamada HTTP.
4. Queremos iniciar o runbook, pelo que deverá clicar em **Iniciar** e, em seguida, clique em **Ok** quando o painel Iniciar Runbook for aberto.<br><br> ![Botão Iniciar](media/automation-first-runbook-textual-powershell/automation-runbook-controls-start.png)<br>    
5. É aberto um painel de tarefas para a tarefa de runbook que criámos. Podemos fechar este painel, mas neste caso, deixamos aberto para vermos o progresso da tarefa.
6. O estado da tarefa é mostrado no **Resumo da Tarefa** e corresponde aos estados que vimos quando testamos o runbook.<br><br> ![Resumo da Tarefa](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)<br>  
7. Quando o estado de runbook mostrar *Concluído*, clique em **Resultado**. Quando o painel Resultado for aberto e for possível ver *Olá, Mundo*.<br><br> ![Resultado da Tarefa](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)<br> 
8. Feche o painel Resultado.
9. Clique em **Todos os Registos** para abrir o painel Fluxos da tarefa de runbook. Apenas devemos conseguir ver *Olá, Mundo* no fluxo de saída, mas isto pode mostrar outros fluxos de uma tarefa de runbook, tais como Verboso e Erro se o runbook escrever nos mesmos.<br><br> ![Todos os Registos](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)<br>   
10. Feche o painel de Fluxos e o painel Tarefas para regressar ao painel MyFirstRunbook-PowerShell.
11. Clique em **Tarefas** para abrir o painel Tarefas para este runbook. Isto apresenta uma lista de todas as tarefas criadas por este runbook. Apenas devemos conseguir ver uma tarefa listada, uma vez que apenas executamos a tarefa uma vez.<br><br> ![Lista de Tarefas](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  
12. Pode clicar nesta tarefa para abrir o mesmo painel Tarefas que visualizamos quando iniciamos o runbook. Isto permite-lhe voltar atrás no tempo e ver os detalhes de qualquer tarefa que foi criada para um determinado runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Passo 5 – adicionar autenticação para gerir os recursos do Azure
Testamos e publicamos o nosso runbook, mas, até ao momento, não faz nada de útil. Queremos que efetue a gestão dos recursos do Azure. No entanto, não será possível fazê-lo embora, a menos que seja autenticado utilizando as credenciais referidas nos [pré-requisitos](#prerequisites). Fazemos isso com o cmdlet **Add-AzureRmAccount**.

1. Abra o editor de texto clicando em **Editar** no painel MyFirstRunbook-PowerShell.<br><br> ![Editar o Runbook](media/automation-first-runbook-textual-powershell/automation-runbook-controls-edit.png)<br>   
2. Já não precisamos da linha **Write-Output**, por isso pode eliminá-la.
3. Escreva ou copie e cole o seguinte código que processa a autenticação com a conta da Execução da Automatização como:
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
   <br>
4. Clique em **Painel de teste** para que possamos testar o runbook.
5. Clique em **Iniciar** para iniciar o teste. Depois de terminar, deverá receber um resultado parecido ao seguinte que mostra informações básicas da sua conta. Isto confirma que a credencial é válida.<br><br> ![Autenticar](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Passo 6 – adicionar código para iniciar uma máquina virtual
Agora que o nosso runbook está a autenticar para a nossa subscrição do Azure, podemos gerir os recursos. Adicionamos um comando para iniciar uma máquina virtual. Pode escolher qualquer máquina virtual na sua subscrição do Azure e, por agora, iremos codificar esse nome no runbook.

1. Depois de *Add-AzureRmAccount*, escreva *Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'NameofResourceGroup'* fornecendo o nome e o nome do Grupo de Recursos da máquina virtual para iniciar.  
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```
   <br>
2. Guarde o runbook e, em seguida, clique no **painel de Teste** para que o possamos testar.
3. Clique em **Iniciar** para iniciar o teste. Depois de terminar, verifique se a máquina virtual foi iniciada.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Passo 7 – adicionar um parâmetro de entrada ao runbook
Atualmente, o nosso runbook inicia a virtual máquina que codificamos no runbook, mas seria mais útil se especificássemos a máquina virtual quando o runbook é iniciado. Iremos agora adicionar parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. Adicione parâmetros a *VMName* e *ResourceGroupName* ao runbook e utilize estas variáveis com o cmdlet **Start-AzureRmVM** como no exemplo abaixo.  
   
   ```
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```
   <br>
2. Guarde o runbook e abra o painel de Teste. Agora pode fornecer valores para as duas variáveis de entrada que são utilizadas no teste.
3. Feche o painel de Teste.
4. Clique em **Publicar** para publicar a nova versão do runbook.
5. Pare a máquina virtual que iniciou no passo anterior.
6. Clique em **Iniciar** para iniciar o runbook. Escreva **VMName** e **ResourceGroupName** na máquina virtual que vai iniciar.<br><br> ![Transmitir Parâmetro](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
7. Depois de o runbook terminar, verifique se a máquina virtual foi iniciada.

## <a name="differences-from-powershell-workflow"></a>Diferenças do Fluxo de Trabalho do PowerShell
Os runbooks do PowerShell têm o mesmo ciclo de vida, capacidades e gestão que os runbooks do Fluxo de trabalho do PowerShell, mas existem algumas diferenças e limitações:

1. Os runbooks do PowerShell são executados rapidamente em comparação com os runbooks do Fluxo de Trabalho do PowerShell, uma vez que não têm o passo de compilação.
2. Os runbooks do Fluxo de Trabalho do PowerShell suportam pontos de verificação. Através dos pontos de verificação, os runbooks do Fluxo de Trabalho do PowerShell podem retomar a partir de qualquer ponto no runbook, ao passo que os runbooks do PowerShell só podem retomar a partir do início.
3. Os runbooks do Fluxo de Trabalho do PowerShell suportam a execução paralela e em série, enquanto os runbooks do PowerShell só podem executar comandos serialmente.
4. Num runbook de Fluxo de Trabalho do PowerShell, uma atividade, um comando ou um bloco de scripts podem ter o seu próprio espaço de execução, ao passo que num runbook do PowerShell, tudo num script é executado num único espaço de execução. Também existem algumas [diferenças sintáticas](https://technet.microsoft.com/magazine/dn151046.aspx) entre um runbook de PowerShell nativo e um runbook de Fluxo de Trabalho do PowerShell.

## <a name="next-steps"></a>Passos seguintes
* Para começar com runbooks Gráficos, consulte o artigo [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
* Para saber mais sobre os tipos de runbook, as vantagens e limitações, consulte o artigo [Tipos de runbook da Automatização do Azure](automation-runbook-types.md)
* Para mais informações sobre a funcionalidade de suporte de scripts do PowerShell, consulte o artigo [Suporte de scripts do PowerShell Nativo na Automatização do Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)


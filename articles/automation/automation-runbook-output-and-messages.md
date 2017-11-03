---
title: "Resultados de Runbook e mensagens na automatização do Azure | Microsoft Docs"
description: "Desribes como criar e obter resultados e erro mensagens a partir de runbooks na automatização do Azure."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 13a414f5-0e2c-4be2-9558-a3e3ec84b6b2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/11/2016
ms.author: magoedte;bwren
ms.openlocfilehash: d0948f25cbb4f661cee4611fb5f7d4d22c9eeec1
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Resultados de Runbook e mensagens na automatização do Azure
A maioria dos runbooks de automatização do Azure terá alguma forma de resultado como uma mensagem de erro para o utilizador ou um objeto complexo destinado a ser consumidos por outro fluxo de trabalho. O Windows PowerShell oferece [vários fluxos](http://blogs.technet.com/heyscriptingguy/archive/2014/03/30/understanding-streams-redirection-and-write-host-in-powershell.aspx) para enviar o resultado de um script ou o fluxo de trabalho. A automatização do Azure funciona com cada um destes fluxos de forma diferente e devem seguir as melhores práticas utilizar cada quando estiver a criar um runbook.

A tabela seguinte fornece uma breve descrição de cada um dos fluxos e do respetivo comportamento no Portal de gestão do Azure, quando executar um runbook publicado e quando [testar um runbook](automation-testing-runbook.md). Existem mais detalhes sobre cada fluxo são fornecidos nas secções subsequentes.

| Fluxo | Descrição | Publicado | Teste |
|:--- |:--- |:--- |:--- |
| Saída |Objetos que se destinam a ser consumidos por outros runbooks. |Escrito no histórico da tarefa. |Apresentado no painel de resultados do teste. |
| Aviso |Mensagem de aviso para o utilizador. |Escrito no histórico da tarefa. |Apresentado no painel de resultados do teste. |
| Erro |Mensagem de erro para o utilizador. Ao contrário de uma exceção, o runbook continua após uma mensagem de erro por predefinição. |Escrito no histórico da tarefa. |Apresentado no painel de resultados do teste. |
| Verboso |Mensagens de fornecer informações gerais ou depuração. |Escrito no histórico da tarefa apenas se o registo verboso está ativado runbook. |Apresentado no painel de resultados do teste apenas se $VerbosePreference está definido para continuar o runbook. |
| Progresso |Registos gerados automaticamente antes e após cada atividade no runbook. O runbook não deve tentar criar os seus próprios registos de progresso, uma vez que estes se destinam a ser um utilizador interativo. |Escrito no histórico da tarefa apenas se o registo de progresso está ativado para o runbook. |Não é apresentado no painel de resultados do teste. |
| Depurar |Mensagens destinadas para um utilizador interativo. Não devem ser utilizadas em runbooks. |Não é escrito no histórico de tarefas. |Não é escrito no painel de resultados do teste. |

## <a name="output-stream"></a>Fluxo de saída
O fluxo de saída destina-se a saída de objetos criados por um script ou um fluxo de trabalho quando é executada corretamente. Na automatização do Azure, este fluxo é utilizado sobretudo para objetos que se destinam a ser consumidos por [principal os runbooks que chamem o runbook atual](automation-child-runbooks.md). Quando lhe [chamar um runbook inline](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution) a partir de um runbook de principal, são devolvidos dados do fluxo de saída ao principal. Só deve utilizar o fluxo de saída para comunicar informações gerais ao utilizador se souber que o runbook nunca irá ser chamado por outro runbook. Como melhor prática, no entanto, deve normalmente utiliza o [fluxo verboso](#Verbose) para comunicar informações gerais ao utilizador.

Pode escrever dados para o fluxo de saída através de [Write-Output](http://technet.microsoft.com/library/hh849921.aspx) ou ao colocar o objeto na sua própria linha no runbook.

    #The following lines both write an object to the output stream.
    Write-Output –InputObject $object
    $object

### <a name="output-from-a-function"></a>Saída de uma função
Quando escrever o fluxo de saída numa função que está incluída no runbook, o resultado é passado para o runbook. Se o runbook atribuir essa saída a uma variável, em seguida, não será escrita no fluxo de saída. Irá escrever quaisquer outros fluxos a partir de dentro da função a escrita no fluxo correspondente para o runbook.

Considere o runbook de exemplo seguinte.

    Workflow Test-Runbook
    {
        Write-Verbose "Verbose outside of function" -Verbose
        Write-Output "Output outside of function"
        $functionOutput = Test-Function
        $functionOutput

    Function Test-Function
     {
        Write-Verbose "Verbose inside of function" -Verbose
        Write-Output "Output inside of function"
      }
    }


O fluxo de saída da tarefa de runbook seria:

    Output inside of function
    Output outside of function

O fluxo verboso da tarefa de runbook seria:

    Verbose outside of function
    Verbose inside of function

Uma vez ter publicou o runbook e antes de começar, tem também ativar o registo verboso nas definições de runbook para obter o resultado de fluxo verboso.

### <a name="declaring-output-data-type"></a>Tipo de dados de saída declarativo
Um fluxo de trabalho, pode especificar o tipo de dados das respetivas saídas ao utilizar o [atributo OutputType](http://technet.microsoft.com/library/hh847785.aspx). Este atributo não tem efeito durante o tempo de execução, mas disponibiliza uma indicação para o autor de runbook no momento da conceção da saída prevista do runbook. Como o conjunto de ferramentas dos runbooks continua a evoluir, a importância de declarar os tipos de dados de saída no momento da conceção será aumentam a importância. Como resultado, é uma melhor prática incluir esta declaração em todos os runbooks que criar.

Eis uma lista de exemplo os tipos de saída:

* String
* System. Int32
* System.Collections.Hashtable
* Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine

O runbook de exemplo seguintes produz um objeto de cadeia e inclui uma declaração do respetivo tipo de saída. Se o runbook devolve uma matriz de um determinado tipo, em seguida, deve ainda especificar o tipo, por oposição a uma matriz do tipo.

    Workflow Test-Runbook
    {
       [OutputType([string])]

       $output = "This is some string output."
       Write-Output $output
    }

Para declarar um tipo de saída em runbooks Grapical ou gráfico fluxo de trabalho do PowerShell, pode selecionar o **entrada e saída** opção de menu e escreva o nome do tipo de saída.  Recomendamos que utilize o nome de classe de .NET completo para torná-lo facilmente identificável ao referenciá-lo a partir de um runbook principal.  Isto apresenta todas as propriedades dessa classe para o barramento de dados no runbook e proporciona muita flexibilidade quando utilizá-los como lógica condicional, registo e de referência como valores para outras atividades no runbook.<br> ![Opção de entrada do Runbook e de saída](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

No exemplo seguinte, temos de dois runbooks gráficos para demonstrar a esta funcionalidade.  Se aplicar o modelo de design do runbook modulares, temos um runbook que funciona como o *modelo do Runbook de autenticação* gerir a autenticação com o Azure com a conta Run As.  Nosso runbook segundo, que normalmente iria efetuar a lógica de núcleos para automatizar um determinado cenário, neste caso, irá executar o *modelo do Runbook de autenticação* e apresentar os resultados para o **teste** painel de resultados.  Em circunstâncias normais, iremos teria este runbook fazer algo em relação a um recurso tirar partido de saída do runbook subordinado.    

Eis a lógica básica do **AuthenticateTo Azure** runbook.<br> ![Exemplo de modelo do Runbook de autenticar](media/automation-runbook-output-and-messages/runbook-authentication-template.png).  

Inclui o tipo de saída *Microsoft.Azure.Commands.Profile.Models.PSAzureContext*, que irá devolver a autenticação propriedades de perfil.<br> ![Exemplo de tipo de resultado do Runbook](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png) 

Embora este runbook seja muito diretamente reencaminhar, há um item de configuração para chamar aqui.  A última atividade está a executar o **Write-Output** cmdlet e escreve os dados de perfil a uma variável de _ $ utilizando uma expressão do PowerShell para o **Inputobject** parâmetro, que é necessário para este cmdlet.  

Para o runbook segundo neste exemplo, com o nome *teste ChildOutputType*, temos simplesmente duas atividades.<br> ![Tipo de Runbook de saída subordinado de exemplo](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png) 

As chamadas de atividade primeiro o **AuthenticateTo Azure** runbook e a segunda atividade está em execução a **Write-Verbose** cmdlet com o **origem de dados** de **saída da atividade** e o valor de **caminho do campo** é **Context.Subscription.SubscriptionName**, que é especificar o resultado de contexto do **AuthenticateTo Azure** runbook.<br> ![Origem de dados de parâmetro de cmdlet Write-Verbose](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)    

A saída resultante é o nome da subscrição.<br> ![Resultados de Runbook de teste ChildOutputType](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

Uma nota sobre o comportamento do controlo de tipo de saída.  Quando escreve um valor no campo do tipo de saída no painel de propriedades de entrada e saída, tem de clicar em fora do controlo depois de escrever, por ordem para a entrada de reconhecido pelo controlo.  

## <a name="message-streams"></a>Fluxos de mensagens
Ao contrário do fluxo de saída, fluxos de mensagens destinam-se para comunicar informações ao utilizador. Existem vários fluxos de mensagens para diferentes tipos de informações e cada um é processada de forma pela automatização do Azure.

### <a name="warning-and-error-streams"></a>Fluxos de avisos e erros
Os fluxos de avisos e erros destinam-se a registar problemas que ocorrem num runbook. São escritos no histórico da tarefa quando um runbook é executado e estão incluídos no painel de resultados do teste no Portal de gestão do Azure, quando um runbook é testado. Por predefinição, o runbook continuará a ser executado após um aviso ou erro. Pode especificar que o runbook deve ser suspenso após um aviso ou erro ao definir um [variável de preferência](#PreferenceVariables) no runbook antes de criar a mensagem. Por exemplo, para fazer com que um runbook a suspender um erro que teria uma exceção, defina **$ErrorActionPreference** para parar.

Criar um aviso ou erro mensagem através de [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) ou [Write-Error](http://technet.microsoft.com/library/hh849962.aspx) cmdlet. As atividades também podem escrever nestes fluxos.

    #The following lines create a warning message and then an error message that will suspend the runbook.

    $ErrorActionPreference = "Stop"
    Write-Warning –Message "This is a warning message."
    Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."

### <a name="verbose-stream"></a>Fluxo verboso
O fluxo de mensagens verbosas destina-se a informações gerais sobre o funcionamento do runbook. Uma vez que o [fluxo de depuração](#Debug) não está disponível num runbook, as mensagens verbosas devem ser utilizadas para informações de depuração. Por predefinição, as mensagens verbosas de runbooks publicados não serão armazenadas no histórico da tarefa. Para armazenar as mensagens verbosas, configure os runbooks publicados para criar registos verbosos no separador Configurar do runbook no Portal de gestão do Azure. Na maioria dos casos, deve manter a predefinição de não criar registos verbosos para um runbook por motivos de desempenho. Ative esta opção apenas para resolver problemas ou depurar um runbook.

Quando [testar um runbook](automation-testing-runbook.md), as mensagens verbosas não são apresentadas, mesmo que o runbook esteja configurado para criar registos verbosos. Para apresentar mensagens verbosas enquanto [testar um runbook](automation-testing-runbook.md), tem de definir a variável de $VerbosePreference para continuar. Com essa variável definida, as mensagens verbosas serão apresentadas no painel de resultados do teste do portal do Azure.

Criar um através de mensagens verbosas de [Write-Verbose](http://technet.microsoft.com/library/hh849951.aspx) cmdlet.

    #The following line creates a verbose message.

    Write-Verbose –Message "This is a verbose message."

### <a name="debug-stream"></a>Fluxo de depuração
O fluxo de depuração destina-se com um utilizador interativo e não deve ser utilizado em runbooks.

## <a name="progress-records"></a>Registos de progressos
Se configurar um runbook para registar o progresso regista (no separador Configurar do runbook no portal do Azure), em seguida, um registo será escrito no histórico da tarefa antes e após cada atividade é executada. Na maioria dos casos, deve manter a predefinição de início de sessão não registos de progresso de um runbook para maximizar o desempenho. Ative esta opção apenas para resolver problemas ou depurar um runbook. Ao testar um runbook, não são apresentadas mensagens de progresso, mesmo se o runbook esteja configurado para criar registos de progressos.

O [Write-Progress](http://technet.microsoft.com/library/hh849902.aspx) cmdlet não é válido num runbook, uma vez que este se destina a ser utilizado com um utilizador interativo.

## <a name="preference-variables"></a>Variáveis de preferência
O Windows PowerShell utiliza [variáveis de preferência](http://technet.microsoft.com/library/hh847796.aspx) para determinar como responder a dados enviados para diferentes fluxos de saída. Pode definir estas variáveis num runbook para controlar a forma como este responderá a dados enviados para vários fluxos.

A tabela seguinte lista as variáveis de preferências que podem ser utilizadas em runbooks com os respetivos válido e valores predefinidos. Tenha em atenção que esta tabela inclui apenas os valores que são válidos num runbook. Os valores adicionais são válidos para as variáveis de preferências quando utilizados no Windows PowerShell fora da automatização do Azure.

| Variável | Valor predefinido | Valores válidos |
|:--- |:--- |:--- |
| WarningPreference |Continuar |Parar<br>Continuar<br>SilentlyContinue |
| ErrorActionPreference |Continuar |Parar<br>Continuar<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |Parar<br>Continuar<br>SilentlyContinue |

A tabela seguinte lista o comportamento para os valores de variáveis de preferência que são válidas nos runbooks.

| Valor | Comportamento |
|:--- |:--- |
| Continuar |Regista a mensagem e continua a executar o runbook. |
| SilentlyContinue |Continua a executar o runbook sem registar a mensagem. Isto tem o efeito de ignorar a mensagem. |
| Parar |Regista a mensagem e suspende o runbook. |

## <a name="retrieving-runbook-output-and-messages"></a>Obter resultados de runbook e mensagens
### <a name="azure-portal"></a>Portal do Azure
Pode ver os detalhes de uma tarefa de runbook no portal do Azure, no separador tarefas de um runbook. O resumo da tarefa irá apresentar os parâmetros de entrada e de [fluxo de saída](#Output) para além das informações gerais sobre a tarefa e quaisquer exceções, caso ocorram. O histórico incluirá as mensagens do [fluxo de saída](#Output) e [aviso e erro fluxos](#WarningError) para além de [fluxo verboso](#Verbose) e [registos de progressos](#Progress) se o runbook esteja configurado para criar registos verbosos e registos de progressos.

### <a name="windows-powershell"></a>Windows PowerShell
No Windows PowerShell, pode obter resultados e mensagens de um runbook com o [Get-AzureAutomationJobOutput](https://msdn.microsoft.com/library/mt603476.aspx) cmdlet. Este cmdlet requer o ID da tarefa e tem um parâmetro denominado fluxo onde pode especificar que fluxo para devolver. Pode especificar qualquer para devolver todos os fluxos da tarefa.

O exemplo seguinte inicia um runbook de exemplo e, em seguida, aguarda pela respetiva conclusão. Depois de concluído, o fluxo de saída é recolhido da tarefa.

    $job = Start-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

    $doLoop = $true
    While ($doLoop) {
       $job = Get-AzureRmAutomationJob -ResourceGroupName "ResourceGroup01" `
       –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
       $status = $job.Status
       $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
    }

    Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output
    
    # For more detailed job output, pipe the output of Get-AzureRmAutomationJobOutput to Get-AzureRmAutomationJobOutputRecord
    Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzureRmAutomationJobOutputRecord
    

### <a name="graphical-authoring"></a>Criação de gráficos
Para runbooks gráficos, está disponível no formato de rastreio de nível de atividade registo extra.  Existem dois níveis de rastreio: básico e detalhados.  O rastreio básico, pode ver o início e hora de fim de cada atividade no runbook e informações relacionadas com a quaisquer tentativas de atividade, por exemplo, o número de tentativas e a hora de início da atividade.  Rastreio de detalhado, receberá básico rastreio adição de entrada e saída para cada atividade.  Tenha em atenção que atualmente os registos de rastreio são escritos utilizando o fluxo verboso, pelo que tem de ativar o registo verboso quando ativar o rastreio.  Para runbooks gráficos com rastreio ativado não é necessário para criar registos de progresso, porque o rastreio básico funciona a mesma finalidade e é mais informativo.

![Vista de fluxos de trabalho de criação gráfico](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Pode ver de captura de ecrã acima que, quando ativar verboso registo e rastreio para runbooks gráficos, muito mais informações estão disponíveis na vista de fluxos de trabalho de produção.  Estas informações adicionais podem ser essenciais para a resolução de problemas de produção com um runbook e, por conseguinte, deve apenas ativá-la para essa finalidade e não como uma prática geral.    
Os registos de rastreio podem ser especialmente várias.  Com um runbook gráfico rastreio, pode obter registos de duas a quatro por atividade, dependendo se configurou o rastreio básico ou detalhado.  A menos que estas informações necessárias para acompanhar o progresso de um runbook para resolução de problemas, pode querer manter rastreio desativada.

**Para ativar o rastreio de nível de atividade, execute os seguintes passos.**

1. No Portal do Azure, abra a sua conta da Automatização.
2. Clique no mosaico **Runbooks** para abrir a lista de runbooks.
3. No painel de Runbooks, clique para selecionar um runbook gráfico da sua lista de runbooks.
4. No painel de definições para o runbook selecionado, clique em **registo e rastreio**.
5. O registo e rastreio painel, sob criar registos verbosos, clique em **no** para ativar o registo verboso e rastreio udner nível de actividade, altere o nível de rastreio para **básico** ou **detalhados** com base no nível de rastreio precisa.<br>
   
   ![Registo de criação gráfico e painel de rastreio](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-operations-management-suite-oms-log-analytics"></a>Análise de registos do Microsoft Operations Management Suite (OMS)
Automatização pode enviar as runbook fluxos de trabalho e o estado da tarefa para a sua área de trabalho de análise de registos do Microsoft Operations Management Suite (OMS).  Análise de registos pode,

* Obter conhecimentos aprofundados sobre as tarefas de automatização 
* Acionamento um e-mail ou o alerta com base no seu estado de tarefa de runbook (por exemplo, falha ou suspenso) 
* Escrever consultas avançadas nos seus fluxos de trabalho 
* Correlacionar tarefas em contas de automatização 
* Visualizar o histórico do trabalho ao longo do tempo    

Para obter mais informações sobre como configurar a integração com a análise de registos para recolher, correlacionar e atuar sobre dados da tarefa, consulte [reencaminhar estado da tarefa e fluxos de trabalho da automatização para análise de registos (OMS)](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre a execução dos runbooks, como monitorizar trabalhos de runbooks e outros detalhes técnicos, veja [Track a runbook job (Acompanhar um trabalho de runbook)](automation-runbook-execution.md)
* Para compreender como estruturar e utilizar runbooks subordinados, consulte [runbooks subordinados na automatização do Azure](automation-child-runbooks.md)


---
title: "Aprendizagem de fluxo de trabalho do PowerShell para a automatização do Azure | Microsoft Docs"
description: "Este artigo destina-se como um lesson rápida para autores familiarizados com o PowerShell para compreender as diferenças entre o PowerShell e fluxo de trabalho do PowerShell e conceitos aplicáveis para runbooks de automatização específicas."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 84bf133e-5343-4e0e-8d6c-bb14304a70db
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 6dce88bdd85a28ce05e1621b08a0f4b148b02627
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Conceitos chave do fluxo de trabalho do Windows PowerShell para runbooks de automatização de aprendizagem 
Os Runbooks na automatização do Azure são implementados como fluxos de trabalho do Windows PowerShell.  Um fluxo de trabalho do Windows PowerShell é semelhante a um script do Windows PowerShell, mas com algumas diferenças significativas que podem ser confusas para um novo utilizador.  Embora este artigo destina-se para o ajudar a escrever runbooks com o fluxo de trabalho do PowerShell, recomendamos que escrever runbooks com o PowerShell, a menos que precisa de pontos de verificação.  Existem várias diferenças de sintaxe durante a criação de runbooks do fluxo de trabalho do PowerShell e estas diferenças requerem um pouco mais trabalho escrever fluxos de trabalho efetivos.  

Um fluxo de trabalho é uma sequência de passos programados e ligados que efetuam tarefas de longa execução ou requerem a coordenação de vários passos em vários dispositivos ou nós geridos. As vantagens de um fluxo de trabalho através de um script normal incluem a capacidade de efetuar simultaneamente uma ação em vários dispositivos e a capacidade de recuperar automaticamente de falhas. Um fluxo de trabalho do Windows PowerShell é um script do Windows PowerShell que utiliza o Windows Workflow Foundation. Apesar do fluxo de trabalho é escrito com a sintaxe do Windows PowerShell e lançado pelo Windows PowerShell, é processada pelo Windows Workflow Foundation.

Para obter detalhes completos sobre os tópicos neste artigo, consulte [introdução ao fluxo de trabalho do Windows PowerShell](http://technet.microsoft.com/library/jj134242.aspx).

## <a name="basic-structure-of-a-workflow"></a>Estrutura básica de um fluxo de trabalho
O primeiro passo para a conversão de um script do PowerShell para um fluxo de trabalho do PowerShell é envolvente-o com o **fluxo de trabalho** palavra-chave.  Um fluxo de trabalho começa com o **fluxo de trabalho** palavra-chave seguida do corpo do script entre chavetas. O nome do fluxo de trabalho segue-se a **fluxo de trabalho** palavra-chave conforme mostrado na seguinte sintaxe:

    Workflow Test-Workflow
    {
       <Commands>
    }

O nome do fluxo de trabalho tem de corresponder ao nome do runbook de automatização. Se o runbook está a ser importado, então o nome de ficheiro deve corresponder ao nome do fluxo de trabalho e deve terminar em *. ps1*.

Para adicionar parâmetros ao fluxo de trabalho, utilize o **Param** palavra-chave tal como faria para um script.

## <a name="code-changes"></a>Alterações do código
Código de fluxo de trabalho do PowerShell procura quase idêntico ao código de script do PowerShell, exceto para algumas alterações significativas.  As secções seguintes descrevem as alterações que necessita para fazer um script do PowerShell para o mesmo para ser executada num fluxo de trabalho.

### <a name="activities"></a>Atividades
Uma atividade é uma tarefa específica num fluxo de trabalho. Tal como um script é composto por um ou mais comandos, um fluxo de trabalho é composto por uma ou mais atividades que são executadas numa sequência. O fluxo de trabalho do Windows PowerShell converte automaticamente muitos dos cmdlets do Windows PowerShell para atividades quando executa um fluxo de trabalho. Quando especificar um destes cmdlets no runbook, a atividade correspondente é executada pelo Windows Workflow Foundation. No caso dos cmdlets sem atividade correspondente, o fluxo de trabalho do Windows PowerShell executa automaticamente o cmdlet dentro de um [InlineScript](#inlinescript) atividade. Não há um conjunto de cmdlets que são excluídos e não pode ser utilizados num fluxo de trabalho, a menos que sejam explicitamente incluídos num bloco InlineScript. Para obter mais detalhes sobre estes conceitos, consulte [utilizar atividades em fluxos de trabalho de Script](http://technet.microsoft.com/library/jj574194.aspx).

Atividades de fluxo de trabalho partilham um conjunto de parâmetros comuns para configurar o funcionamento deles. Para obter detalhes sobre os parâmetros comuns do fluxo de trabalho, consulte [about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Parâmetros posicionais
Não é possível utilizar os parâmetros posicionais com cmdlets num fluxo de trabalho e atividades.  Este significa que é que tem de utilizar os nomes dos parâmetros.

Por exemplo, considere o seguinte código que obtém todos os serviços em execução.

     Get-Service | Where-Object {$_.Status -eq "Running"}

Se tentar executar este código mesmo num fluxo de trabalho, receberá uma mensagem como "conjunto de parâmetros não pode ser resolvido utilizando parâmetros nomeados especificados."  Para corrigir isto, forneça o nome do parâmetro como no seguinte.

    Workflow Get-RunningServices
    {
        Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
    }

### <a name="deserialized-objects"></a>Objetos de serialização anulados
Objetos em fluxos de trabalho são anular a serialização.  Isto significa que as respetivas propriedades ainda estão disponíveis, mas não os respetivos métodos.  Por exemplo, considere o seguinte código de PowerShell para um serviço utilizando o método de paragem do objeto de serviço.

    $Service = Get-Service -Name MyService
    $Service.Stop()

Se tentar executar esta num fluxo de trabalho, receberá um erro a indicar "invocação do método não é suportada num fluxo de trabalho do Windows PowerShell."  

Uma das alternativas consiste moldar estas duas linhas de código num [InlineScript](#inlinescript) bloqueie no qual caso $Service seria um objeto de serviço no bloco.

    Workflow Stop-Service
    {
        InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
        }
    }

Outra opção consiste em utilizar outro cmdlet que efetua a mesma funcionalidade que o método, se disponível.  No nosso exemplo, o cmdlet Stop-Service fornece a mesma funcionalidade que o método de paragem e pode utilizar o seguinte para um fluxo de trabalho.

    Workflow Stop-MyService
    {
        $Service = Get-Service -Name MyService
        Stop-Service -Name $Service.Name
    }


## <a name="inlinescript"></a>InlineScript
O **InlineScript** atividade é útil quando tem de executar um ou mais comandos como tradicional script do PowerShell em vez de fluxo de trabalho do PowerShell.  Enquanto os comandos num fluxo de trabalho são enviados para o Windows Workflow Foundation para processamento, os comandos num bloco InlineScript são processados pelo Windows PowerShell.

O InlineScript utiliza a seguinte sintaxe indicada abaixo.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

Pode devolver resultados de uma InlineScript atribuindo a saída para uma variável. O exemplo seguinte para um serviço e, em seguida, devolve o nome do serviço.

    Workflow Stop-MyService
    {
        $Output = InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Pode passar valores num bloco InlineScript, mas tem de utilizar **$Using** modificador do âmbito.  O exemplo seguinte é idêntico do exemplo anterior exceto a que o nome do serviço é fornecido por uma variável.

    Workflow Stop-MyService
    {
        $ServiceName = "MyService"

        $Output = InlineScript {
            $Service = Get-Service -Name $Using:ServiceName
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Enquanto as atividades de InlineScript possam ser fundamentais em determinados fluxos de trabalho, que não suportam construções de fluxo de trabalho e só devem ser utilizadas pelos seguintes motivos:

* Não é possível utilizar [pontos de verificação](#checkpoints) dentro de um bloco InlineScript. Se ocorrer uma falha no bloco, é necessário recomeçar desde o início do bloco.
* Não é possível utilizar [execução paralela](#parallel-processing) dentro de um InlineScriptBlock.
* InlineScript afeta a escalabilidade do fluxo de trabalho porque realiza a sessão do Windows PowerShell durante todo o bloco InlineScript.

Para obter mais informações sobre como utilizar o InlineScript, consulte [executar comandos do Windows PowerShell num fluxo de trabalho](http://technet.microsoft.com/library/jj574197.aspx) e [about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Processamento paralelo
Uma das vantagens dos fluxos de trabalho do Windows PowerShell é a capacidade para executar um conjunto de comandos em paralelo em vez de sequencialmente como com um script normal.

Pode utilizar o **paralelas** palavra-chave para criar um bloco de scripts com vários comandos que são executados em simultâneo. Esta opção utiliza a seguinte sintaxe indicada abaixo. Neste caso, Activity1 e Activity2 inicia-se ao mesmo tempo. Activity3 inicia apenas depois de Activity1 e Activity2 forem concluídos.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


Por exemplo, considere os seguintes comandos do PowerShell que copiar vários ficheiros para um destino de rede.  Estes comandos são executados sequencialmente, para que um ficheiro deve ser concluído antes da próxima é iniciada a copiar.     

    Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
    Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
    Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

O fluxo de trabalho seguinte executa estas mesmos comandos em paralelo para que todos eles começar a copiar ao mesmo tempo.  Apenas depois de estarem todas as copiados é conclusão apresentada a mensagem.

    Workflow Copy-Files
    {
        Parallel
        {
            Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
            Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
            Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
        }

        Write-Output "Files copied."
    }


Pode utilizar o **ForEach-Parallel** construção para processar comandos para cada item numa coleção em simultâneo. Os itens na coleção são processados em paralelo, enquanto os comandos no bloco de script são executados sequencialmente. Esta opção utiliza a seguinte sintaxe indicada abaixo. Neste caso, Activity1 começa em simultâneo de todos os itens na coleção. Para cada item, Activity2 é iniciado depois de Activity1 está concluída. Activity3 inicia apenas depois de Activity1 e Activity2 concluiu para todos os itens.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

O exemplo seguinte é semelhante ao anterior exemplo copiar ficheiros em paralelo.  Neste caso, é apresentada uma mensagem para cada ficheiro depois copia.  Apenas depois de estarem todas as completamente copiados é conclusão final apresentada a mensagem.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach -Parallel ($File in $Files)
        {
            Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
        }

        Write-Output "All files copied."
    }

> [!NOTE]
> Não recomendamos a execução de runbooks subordinados em paralelo, uma vez que tal tem foi apresentado para dar resultados pouco fiáveis.  O resultado do runbook subordinado, por vezes, não for apresentada, e definições no subordinado de um runbook podem afetar outros runbooks subordinados paralela
>

## <a name="checkpoints"></a>Pontos de verificação
A *ponto de verificação* é um instantâneo do estado atual do fluxo de trabalho que inclui o valor atual para variáveis e qualquer resultado gerado até esse ponto. Se um fluxo de trabalho termina no registo de erros ou está suspenso, em seguida, da próxima vez que é executado será iniciada do último ponto de verificação em vez do início do worfklow.  Pode definir um ponto de verificação num fluxo de trabalho com o **Checkpoint-Workflow** atividade.

No seguinte código de exemplo, ocorre uma exceção após Activity2 causando o fluxo de trabalho terminar. Quando o fluxo de trabalho é executado novamente, começa por executar Activity2 porque foi imediatamente depois do último ponto de verificação definido.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

Deve definir pontos de verificação num fluxo de trabalho após as atividades que poderão estar expostos a exceção e não devem ser repetido se o fluxo de trabalho é retomado. Por exemplo, o fluxo de trabalho pode criar uma máquina virtual. Pode configurar um ponto de verificação antes e depois dos comandos para criar a máquina virtual. Se a criação falhar, em seguida, os comandos iriam ser repetidos se o fluxo de trabalho for iniciado novamente. Se o worfklow falhar após a criação for bem sucedida, em seguida, a máquina virtual não será criada novamente quando o fluxo de trabalho é retomado.

O exemplo seguinte copia vários ficheiros para uma localização de rede e define um ponto de verificação após cada ficheiro.  Se a localização de rede se tenha perdida, em seguida, o fluxo de trabalho termina no registo de erros.  Quando é iniciada novamente, será retomada no último ponto de verificação, o que significa que apenas os ficheiros que já foram copiados são ignorados.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach ($File in $Files)
        {
            Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
            Checkpoint-Workflow
        }

        Write-Output "All files copied."
    }

Porque as credenciais de nome de utilizador não são mantidas depois de chamar o [Suspend-Workflow](https://technet.microsoft.com/library/jj733586.aspx) atividade ou após o último ponto de verificação, tem de definir as credenciais para nulo e, em seguida, obtê-las novamente a partir da loja asset após  **Suspend-Workflow** ou ponto de verificação é chamado.  Caso contrário, poderá receber a seguinte mensagem de erro: *não é possível retomar a tarefa de fluxo de trabalho, porque os dados de persistência não foi possível ser completamente guardados ou guardar dados de persistência tem foi danificados. Tem de reiniciar o fluxo de trabalho.*

O mesmo código seguinte demonstra como lidar com isto nos runbooks do fluxo de trabalho do PowerShell.

    workflow CreateTestVms
    {
       $Cred = Get-AzureAutomationCredential -Name "MyCredential"
       $null = Add-AzureRmAccount -Credential $Cred

       $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

       foreach ($VmName in $VmsToCreate)
         {
          # Do work first to create the VM (code not shown)

          # Now add the VM
          New-AzureRmVm -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

          # Checkpoint so that VM creation is not repeated if workflow suspends
          $Cred = $null
          Checkpoint-Workflow
          $Cred = Get-AzureAutomationCredential -Name "MyCredential"
          $null = Add-AzureRmAccount -Credential $Cred
         }
     }


Isto não é necessário se estão a autenticar utilizando uma conta Run as configuradas com um principal de serviço.  

Para obter mais informações sobre pontos de verificação, consulte [adicionar pontos de verificação para um fluxo de trabalho de Script](http://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>Passos seguintes
* Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)

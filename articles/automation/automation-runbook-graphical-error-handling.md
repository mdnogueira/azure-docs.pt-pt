---
title: "Erro ao Processar em Runbooks Gráficos da Automatização do Azure | Microsoft Docs"
description: "Este artigo descreve como implementar a lógica de processamento de erros em runbooks gráficos da Automatização do Azure."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/26/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: f9b359691122da9e5d93e51f3085cad51e55d8f2
ms.openlocfilehash: 13c3e1693badcf4148738cb63666f34546d1696c

---

# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Erro ao Processar em Runbooks Gráficos da Automatização do Azure

Um dos principais fatores de design de runbooks que deve considerar é a capacidade de identificar os diferentes problemas que os runbooks podem experienciar, como êxito, estados de erro esperados e condições de erros inesperadas.  Os runbooks devem incluir o processamento de erros para fazer a deteção em conformidade.  Com os runbooks gráficos, se quisesse validar o resultado de uma atividade ou processar um erro de forma apropriada, possivelmente, seguiria com uma atividade de código do PowerShell e definiria a lógica condicional na ligação de resultado da atividade ou, em alternativa, aplicaria outro método.          

Muitas vezes, quando os runbooks são executados, se ocorrer um erro de não terminação numa atividade, qualquer atividade que se lhe siga será processada, de qualquer forma.  Obviamente, é provável que gere uma exceção, mas a ideia fundamental é que a atividade seguinte pode ser executada. O motivo por trás deste comportamento deve-se à forma como a linguagem do PowerShell foi concebida para processar erros.    

Os tipos de erros do PowerShell que podem ocorrer durante a execução são de terminação ou não terminação.  As diferenças são as seguintes:

* Erro de terminação: um erro grave durante a execução que para completamente o comando (ou a execução do script). Alguns exemplos incluem cmdlets não existentes, erros de sintaxe que impedem a execução de cmdlets ou outros erros fatais.

* Erro de não terminação: um erro não grave que permite a continuação da execução, apesar da falha. De entre os exemplos, incluem-se erros operacionais, como ficheiros não encontrados, problemas de permissões, etc.

Os runbooks gráficos da Automatização do Azure foram melhorados com a capacidade de incluir o processamento de erros, para que possa tornar as exceções em erros de não terminação e criar ligações de erros entre atividades. Desta forma, o autor de um runbook consegue apanhar erros e receber um caminho para gerir a condição verificada ou inesperada.  

## <a name="when-to-use"></a>Quando utilizar

Controlar a execução dos fluxos de trabalho é importante para garantir que, sempre que existe uma atividade crítica que produz um erro ou exceção, é possível impedir o avanço para a atividade seguinte no runbook e lidar com o erro adequadamente.  Isto é especialmente necessário se os seus runbooks suportarem um processo de operações empresariais ou de serviços.

Para cada atividade que pode produzir um erro, o autor do runbook pode adicionar uma ligação de erro que aponte para outra atividade qualquer.  A atividade de destino pode ser de qualquer tipo: atividade de código, invocar um cmdlet, invocar outro runbook ou outra qualquer. 

Além disso, a atividade de destino também pode ter ligações de saída, que podem ser ligações normais ou de erro, para que o autor do runbook possa implementar uma lógica de processamento de erros sem ter de recorrer à inclusão de uma atividade de código.  Embora a prática recomendada seja criar um runbook de processamento de erros dedicado com funcionalidade comum, não é obrigatória e a lógica de processamento de erros em atividades de código do PowerShell não é a única alternativa.  

Como exemplo, considere um runbook que tenta iniciar uma VM e instalar uma aplicação na mesma, mas se não for iniciada corretamente, realiza duas ações: 

1. Enviar uma notificação sobre o problema.
2. Iniciar outro runbook que aprovisiona automaticamente uma VM nova. 

Uma solução seria ter uma ligação de erro a apontar para uma atividade para processar o passo 1, como o cmdlet **Write-Warning**, ligado a uma atividade para o passo 2, como o cmdlet **Start-AzureRmAutomationRunbook**. 

Também pode generalizar este comportamento para ser utilizado em muitos runbooks e colocar estas duas atividades num runbook de processamento de erros separado, seguindo as orientações sugeridas anteriormente.  Antes de chamar este runbook de processamento de erros, poderia criar uma mensagem personalizada a partir dos dados do runbook original e, em seguida, transmiti-la como parâmetro para aquele. 

## <a name="how-to-use"></a>Como utilizar

Cada atividade tem uma configuração para tornar as exceções em erros de não terminação. Esta configuração está desativada, por predefinição.  Deve ativá-la na atividade em que pretende processar erros.  Ao ativar esta configuração, vai garantir que quer os erros de terminação, quer de não terminação, na atividade serão processados como erros de não terminação e poderão, depois, ser tratados com uma ligação de erro.  Depois de configurar esta definição, crie uma atividade que processe o erro.  Se uma atividade produzir um erro, as ligações de erro de saída serão seguidas, mas as ligações normais não, mesmo se a atividade produzir também uma saída normal.<br><br> ![Exemplo de Ligação de Erro de Runbook de Automatização](media/automation-runbook-graphical-error-handling/error-link-example.png)

No exemplo simples seguinte, temos um runbook que obtém uma variável que contém o nome de computador de uma máquina virtual e, depois, tenta iniciá-la com a atividade seguinte.<br><br> ![Exemplo de Processamento de Erro de Runbook de Automatização](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

As atividades **Get-AutomationVariable** e **Start-AzureRmVm** estão configuradas para converter exceções em erros.  Se ocorrerem problemas ao obter a variável ou ao iniciar a VM, serão gerados erros.<br><br> ![Definições de Atividade de Processamento de Erro de Runbook de Automatização](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

As ligações de erro são enviadas destas atividades para uma única atividade **Error Management** (uma atividade de Código), que é configurada com uma expressão simples do PowerShell mediante a utilização da palavra-chave *Throw*, para parar o processamento, juntamente com *$Error.Exception.Message*, para obter a mensagem que descreve a exceção atual.<br><br> ![Exemplo de Código de Processamento de Erro de Runbook de Automatização](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre as ligações e compreender os tipos de ligações em runbooks gráficos, veja [Graphical Authoring in Azure Automation (Criação de Runbooks Gráficos na Automatização do Azure)](automation-graphical-authoring-intro.md#links-and-workflow).

* Para saber mais sobre a execução dos runbooks, como monitorizar trabalhos de runbooks e outros detalhes técnicos, veja [Track a runbook job (Acompanhar um trabalho de runbook)](automation-runbook-execution.md) 


<!--HONumber=Jan17_HO1-->



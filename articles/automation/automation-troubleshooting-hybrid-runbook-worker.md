---
title: "Resolver problemas de trabalho de Runbook híbrida da automatização do Azure | Microsoft Docs"
description: "Descrevem os sintomas, as causas e resolução de problemas mais comuns do Runbook Worker híbrido na automatização do Azure."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 02c6606e-8924-4328-a196-45630c2255e9
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: magoedte
ms.openlocfilehash: 1a72fcf2493396b4744a49b01f3c6da8eed0de40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-tips-for-hybrid-runbook-worker"></a>Sugestões de resolução de problemas do Runbook Worker híbrido

Este artigo fornece erros podem ocorrer com automatização de Runbook híbridos e sugere possíveis soluções para resolvê-los de resolução de problemas.

## <a name="a-runbook-job-terminates-with-a-status-of-suspended"></a>Uma tarefa de runbook termina com o estado suspenso

O runbook está suspenso pouco tempo depois tentar executá-lo três vezes. Existem condições que poderão interromper o runbook a conclusão com êxito e a mensagem de erro relacionados não inclui qualquer informação adicional que indica o motivo. Este artigo fornece os passos de resolução de problemas para problemas relacionados com as falhas de execução de runbook do Runbook Worker híbrido.

Se o problema do Azure não esteja endereçado neste artigo, visite os fóruns do Azure no [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Pode publicar o seu problema estes fóruns ou a [ @AzureSupport no Twitter](https://twitter.com/AzureSupport). Além disso, pode ficheiro um pedido de suporte do Azure ao selecionar **obter suporte** no [suporte do Azure](https://azure.microsoft.com/support/options/) site.

### <a name="symptom"></a>Sintoma
Falha de execução do Runbook e o erro devolvido é, "a ação de tarefa não é possível executar 'Ativar', porque o processo parou inesperadamente. A ação de tarefa foi tentada 3 vezes."

Existem várias causas possíveis para o erro: 

1. A função de trabalho híbrida estiver atrás de um proxy ou firewall
2. A função de trabalho híbrida está em execução no computador tiver menos do que o mínimo [requisitos de hardware](automation-offering-get-started.md#hybrid-runbook-worker)  
3. Os runbooks não é possível autenticar com recursos locais

#### <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>Causa 1: O Runbook Worker híbrido é atrás de proxy ou firewall
O computador que está a executar o Runbook Worker híbrido no estiver atrás de um servidor de firewall ou proxy e acesso de rede de saída não pode ser permitido ou configurado corretamente.

#### <a name="solution"></a>Solução
Certifique-se de que o computador tem acesso de saída para *.azure automation.net na porta 443. 

#### <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>Causa 2: O computador tiver menos de mínimos de hardware
Computadores com o Runbook Worker híbrido devem cumprir os requisitos mínimos de hardware antes de indicar que esta funcionalidade do anfitrião. Caso contrário, consoante a utilização de recursos de outros processos em segundo plano e a contenção causado por runbooks durante a execução, o computador irá ficar sobrecarregado e causar atrasos de tarefa de runbook ou tempos limite. 

#### <a name="solution"></a>Solução
Confirme primeiro o computador designado para executar a funcionalidade do Runbook Worker híbrido cumpre os requisitos mínimos de hardware.  Se existir, monitorize a utilização de CPU e memória para determinar a qualquer correlação entre o desempenho dos processos de trabalho de Runbook híbrida e o Windows.  Se existir memória ou pressão de CPU, isto pode indicar a necessidade de atualizar ou adicione processadores adicionais ou aumente a memória para resolver o congestionamento do recurso e resolva o erro. Em alternativa, selecione um recurso de computação diferentes que pode suportar os requisitos mínimos e escala, quando a carga de trabalho exigências indicam que um aumento é necessário.         

#### <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>Causa 3: Não é possível autenticar Runbooks com recursos locais

#### <a name="solution"></a>Solução
Verifique o **Microsoft SMA** registo de eventos para um evento correspondente com a descrição *Win32 processo abortado com o código [4294967295]*.  A causa deste erro é que ainda não configurada a autenticação nos runbooks ou especificar as credenciais Run As para o grupo de trabalho híbridas.  Reveja [Runbook permissões](automation-hrw-run-runbooks.md#runbook-permissions) para confirmar que configurou corretamente a autenticação para os runbooks.  

## <a name="next-steps"></a>Passos seguintes

Para obter ajuda na resolução de problemas de outros problemas na automatização, consulte [resolução de problemas comuns de automatização do Azure](automation-troubleshooting-automation-errors.md) 
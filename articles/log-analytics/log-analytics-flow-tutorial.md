---
title: "Automatizar processos de análise de registos do Azure com o Microsoft Flow"
description: "Saiba como pode utilizar o Microsoft Flow rapidamente automatizar processos repetíveis, utilizando o conector do Log Analytics do Azure."
services: log-analytics
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: log-analytics
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: bwren
ms.openlocfilehash: 9884b97aad8f022e6bc20b2a630ccdd07ebc64db
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="automate-log-analytics-processes-with-the-connector-for-microsoft-flow"></a>Automatizar processos de análise de registos com o conector Microsoft Flow
[Microsoft Flow](https://ms.flow.microsoft.com) permite-lhe criar fluxos de trabalho automatizados através de centenas de ações para uma variedade de serviços. Resultado de uma ação pode ser utilizado como entrada para outro, permitindo-lhe criar integração entre os diferentes serviços.  O conector do Log Analytics do Azure para a Microsoft Flow permitem-lhe criar fluxos de trabalho que incluem dados obtidos através de pesquisas de registo na análise de registos.

Por exemplo, pode utilizar o Microsoft Flow para utilizar dados de análise de registos uma notificação por e-mail a partir do Office 365, crie um erro no Visual Studio Team Services ou publicar uma mensagem Slack.  Pode acionar um fluxo de trabalho por um agendamento simple ou a partir de qualquer ação num serviço ligado, tais como quando é recebido um e-mail ou um tweet.  

O tutorial este artigo mostra como criar um fluxo que envia automaticamente os resultados da pesquisa de análise de registos de registo por e-mail, apenas um exemplo de como pode utilizar a análise de registos Flow da Microsoft. 


## <a name="step-1-create-a-flow"></a>Passo 1: Criar um fluxo
1. Inicie sessão no [Microsoft Flow](http://flow.microsoft.com)e selecione **flui My**.
2. Clique em **+ criar em branco**.

## <a name="step-2-create-a-trigger-for-your-flow"></a>Passo 2: Criar um acionador para o fluxo
1. Clique em **pesquisa centenas de conectores e acionadores**.
2. Tipo **agenda** na caixa de pesquisa.
3. Selecione **agenda**e, em seguida, selecione **agenda - periodicidade**.
4. No **frequência** selecione **dia** e no **intervalo** box, introduza **1**.<br><br>![Caixa de diálogo de Acionador de Flow Microsoft](media/log-analytics-flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>Passo 3: Adicionar uma ação de análise de registos
1. Clique em **+ novo passo**e, em seguida, clique em **adicionar uma ação**.
2. Procurar **Iniciar análise**.
3. Clique em **Log Analytics do Azure – execute a consulta e visualizar os resultados**.<br><br>![Execute a janela de consulta de análise de registos](media/log-analytics-flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>Passo 4: Configurar a ação de análise de registos

1. Especifique os detalhes para a sua área de trabalho, incluindo o ID de subscrição, grupo de recursos e o nome de área de trabalho.
2. Adicione a seguinte consulta de análise de registos para o **consulta** janela.  Esta é apenas uma consulta de exemplo e pode substituir qualquer outra que devolve dados.
```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
```

2. Selecione **HTML tabela** para o **tipo de gráfico**.<br><br>![Ação de análise do registo](media/log-analytics-flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>Passo 5: Configurar o fluxo para enviar correio eletrónico

1. Clique em **novo passo**e, em seguida, clique em **+ adicionar uma ação**.
2. Procurar **Outlook do Office 365**.
3. Clique em **Office 365 Outlook – enviar um e-mail**.<br><br>![Janela de seleção do Outlook do Office 365](media/log-analytics-flow-tutorial/flow04.png)

4. Especifique o endereço de e-mail de um destinatário no **para** janela e um assunto para o e-mail no **requerente**.
5. Clique em qualquer parte no **corpo** caixa.  A **conteúdo dinâmico** é aberta a janela com os valores das ações de anteriores.  
6. Selecione **corpo**.  Este é o resultado da consulta na ação de análise de registos.
6. Clique em **Mostrar opções avançadas**.
7. No **é HTML** caixa, selecione **Sim**.<br><br>![Janela de configuração de e-mail do Office 365](media/log-analytics-flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>Passo 6: Guardar e testar o fluxo
1. No **nome de fluxo** caixa, adicionar um nome para o fluxo e, em seguida, clique em **criar fluxo**.<br><br>![Guardar fluxo](media/log-analytics-flow-tutorial/flow06.png)
2. O fluxo está agora criado e será executado após um dia em que é a agenda que especificou. 
3. Para testar imediatamente o fluxo, clique em **executar agora** e, em seguida, **executar fluxo**.<br><br>![Executar o fluxo](media/log-analytics-flow-tutorial/flow07.png)
3. Quando o fluxo é concluída, verifique o correio de destinatário que especificou.  Deve ter recebido uma mensagem com um corpo semelhante ao seguinte:<br><br>![E-mail de exemplo](media/log-analytics-flow-tutorial/flow08.png)


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [pesquisas de registo na análise de registos](log-analytics-log-search-new.md).
- Saiba mais sobre [Microsoft Flow](https://ms.flow.microsoft.com).




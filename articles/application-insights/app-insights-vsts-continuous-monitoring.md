---
title: "Monitorização contínua de pipeline de versão do DevOps com VSTS e o Azure Application Insights | Microsoft Docs"
description: "Fornece instruções para configurar rapidamente a monitorização contínua com o Application Insights"
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/13/2017
ms.service: application-insights
ms.topic: article
manager: carmonm
ms.openlocfilehash: 6ced5941d06c02a2576bf7f561bd535810e7ffd9
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Adicionar a monitorização contínua para o pipeline de versão

Serviços de equipa do Visual Studio (VSTS) integra-se com o Azure Application Insights para permitir a monitorização contínua de pipeline de versão do DevOps em todo o ciclo de vida de desenvolvimento de software. 

VSTS agora suporta a monitorização contínua whereby pipelines de versão podem incorporar dados de monitorização do Application Insights e outros recursos do Azure. Quando é detetado um alerta do Application Insights, a implementação pode permanecer gated ou revertida até que o alerta é resolvido. Se todas as verificações de passaram, implementações podem avançar automaticamente de teste para produção sem a necessidade de intervenção manual. 

## <a name="configure-continuous-monitoring"></a>Configurar a monitorização contínua

1. Selecione um projeto de VSTS existente.

2. Coloque o cursor sobre **compilar e versão** > selecione **versões** > clique o **sinal de adição** > **Criar definição de versão** > Procurar **monitorização** > **implementação do App Service do Azure com a monitorização contínua.**

   ![Nova definição de versão VSTS](.\media\app-insights-continuous-monitoring\001.png)

3. Clique em **aplicar.**

4. Junto ao ponto de exclamação vermelho, selecione o texto azul para **ver tarefas do ambiente.**

   ![Tarefas da vista de ambiente](.\media\app-insights-continuous-monitoring\002.png)

   Será apresentada uma caixa de configuração, utilize a tabela seguinte para preencher os campos de entrada.

    | Parâmetro        | Valor |
   | ------------- |:-----|
   | **Nome do ambiente**      | Nome que descreva o ambiente de definição da versão |
   | **Subscrição do Azure** | Pendente preenche com quaisquer subscrições do Azure associadas à conta de VSTS|
   | **Nome do serviço de aplicações** | Introdução manual de um novo valor poderão ser necessária para este campo consoante outros seleções |
   | **Grupo de Recursos**    | Pendente preenche com grupos de recursos disponíveis |
   | **Nome de recurso do Application Insights** | Pendente preenche todos os recursos do Application Insights que correspondem ao grupo de recursos selecionado anteriormente.

5. Selecione **alertas de configurar o Application Insights**

6. Para regras de alerta predefinido, selecione **guardar** > introduza um comentário descritivo > clique **OK**

## <a name="modify-alert-rules"></a>Modificar regras de alertas

1. Para modificar as definições de alerta predefinidas, clique na caixa com **reticências...**  à direita do **regras de alertas.**

   (Existem regras de alerta de out-of-box quatro: disponibilidade, pedidos falhados, tempo de resposta do servidor, exceções de servidor.)

2. Clique o símbolo de lista pendente junto a **disponibilidade.**

3. Modificar a disponibilidade **limiar** para satisfazer os seus requisitos de nível de serviço.

   ![Modificar alerta de](.\media\app-insights-continuous-monitoring\003.png)

4. Selecione **OK** > **guardar** > introduza um comentário descritivo > clique **OK.**

## <a name="add-deployment-conditions"></a>Adicionar condições de implementação

1. Clique em **Pipeline** > selecione o **Pre** ou **condições de pós-implementação** símbolo consoante a etapa que necessita de uma porta de monitorização contínua.

   ![Condições de pré-implementação](.\media\app-insights-continuous-monitoring\004.png)

2. Definir **portas** para **ativado** > **portas de aprovação**> clique **adicionar.**

3. Selecione **Azure Monitor** (esta opção dá-lhe a capacidade para alertas de acesso tanto do Monitor do Azure e o Application Insights)

    ![Azure Monitor](.\media\app-insights-continuous-monitoring\005.png)

4. Introduza um **tempo limite de portas** valor.

5. Introduza um **intervalo de amostragem.**

## <a name="deployment-gate-status-logs"></a>Registos de estado de porta de implementação

Depois de adicionar as portas de implementação, um alerta no Application Insights que excede o limiar definido anteriormente, guards a implementação da versão indesejável promoção. Depois do alerta é resolvido, a implementação para poder continuar automaticamente.

Para reparar este comportamento, selecione **versões** > nome de versão com o botão direito **abrir** > **registos.**

![Registos](.\media\app-insights-continuous-monitoring\006.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre criar VSTS e versão experimente estes [inícios rápidos.](https://docs.microsoft.com/en-us/vsts/build-release/)

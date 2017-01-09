---
title: "Analisar a utilização de dados do Log Analytics | Microsoft Docs"
description: "Pode utilizar o dashboard Utilização do Log Analytics para ver a quantidade de dados que estão a ser enviados para o serviço do OMS."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/02/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 668cde23cb717fcad52fd7823561d10735e6df1b


---
# <a name="analyze-data-usage-in-log-analytics"></a>Analisar a utilização de dados do Log Analytics
O Log Analytics recolhe dados e envia-os para o serviço do OMS periodicamente.  Pode utilizar o dashboard **Utilização do Log Analytics** para ver a quantidade de dados que estão a ser enviados para o serviço do OMS. O dashboard também apresenta a quantidade de dados que estão a ser enviados pelas soluções e a frequência com que os seus servidores estão a enviar dados.

> [!NOTE]
> Se tiver uma conta gratuita, está limitado ao envio de 500 MB de dados para o serviço do OMS diariamente. Se atingir o limite diário, a análise de dados para e é retomada no início do dia seguinte. Nesse caso, terá de reenviar os dados que não tenham sido aceites ou processados pelo OMS.

Se tiver excedido ou estiver quase a atingir o limite de utilização, pode, opcionalmente, remover uma solução para reduzir a quantidade de dados que envia para o serviço do OMS. Para mais informações sobre a remoção de soluções, veja [Adicionar soluções Log Analytics a partir da Galeria de Soluções](log-analytics-add-solutions.md).

![dashboard de utilização](./media/log-analytics-usage/usage-dashboard01.png)

O dashboard **Utilização do Log Analytics** apresenta as informações seguintes:

- Volume de dados
    - Volume de dados ao longo do tempo (baseado no seu âmbito de período de tempo atual)
    - Volume de dados por solução
    - Dados não associados a um computador
- Computadores
    - Computadores que estão a enviar dados
    - Computadores sem dados nas últimas 24 horas
- Ofertas
    - Nós Insight e Análise de Dados
    - Nós de Automatização e Controlo
    - Nós de segurança
- Desempenho
    - Tempo decorrido para recolher e indexar dados
- Lista de consultas

## <a name="to-work-with-usage-data"></a>Trabalhar com dados de utilização
1. Se ainda não o fez, inicie sessão no [portal do Azure](https://portal.azure.com) através da sua subscrição do Azure.
2. No menu **Hub**, clique em **Mais serviços** e, na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Clique em **Log Analytics**.  
    ![Hub do Azure](./media/log-analytics-usage/hub.png)
3. O dashboard do **Log Analytics** mostra uma lista das suas áreas de trabalho. Selecione uma área de trabalho.
4. No dashboard da *área de trabalho*, clique em **Utilização do Log Analytics**.
5. No dashboard **Utilização do Log Analytics**, clique em **Hora: últimas 24 horas**, para alterar o intervalo de tempo.  
    ![intervalo de tempo](./media/log-analytics-usage/time.png)
6. Veja os painéis da categoria de utilização que mostra áreas em que está interessado. Escolha um painel e clique num item no mesmo para ver mais detalhes na [Pesquisa de Registos](log-analytics-log-searches.md).  
    ![exemplo de painel de utilização de dados](./media/log-analytics-usage/blade.png)
7. No dashboard da Pesquisa de Registos, reveja os resultados devolvidos pela pesquisa.  
    ![exemplo de pesquisa de registos de utilização](./media/log-analytics-usage/usage-log-search.png)


## <a name="next-steps"></a>Passos seguintes
* Veja [Log searches in Log Analytics (Pesquisas de registos no Log Analytics)](log-analytics-log-searches.md) para ver informações detalhadas que são recolhidas e enviadas para OMS por funcionalidades e soluções.



<!--HONumber=Dec16_HO1-->



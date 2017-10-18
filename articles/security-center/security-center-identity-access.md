---
title: "Monitorizar a identidade e o acesso no Centro de Segurança do Azure | Microsoft Docs"
description: "Saiba como utilizar a capacidade de identidade e acesso no Centro de Segurança do Azure para monitorizar a atividade de acesso dos seus utilizadores e os problemas relacionados com identidades."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: 842045fbcb5b4f661cc006a207f4087370a7b4ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-identity-and-access-in-azure-security-center"></a>Monitorizar a identidade e o acesso no Centro de Segurança do Azure
Este artigo ajuda-o a utilizar o Centro de Segurança do Azure para monitorizar a atividade de identidade e acesso dos utilizadores.

## <a name="why-monitor-identity-and-access"></a>Porquê monitorizar a identidade e o acesso?
A identidade deve ser o plano de controlo da sua empresa e proteger a sua identidade deve ser a sua prioridade. No passado, existiram perímetros em torno de organizações e esses perímetros foram um dos principais limites de defesa. Hoje em dia, com mais dados e mais aplicações a mudarem para a cloud, a identidade torna-se no novo perímetro.

Através da monitorização das atividades de identidade, poderá levar a cabo ações proativas antes da ocorrência de um incidente ou ações reativas para parar uma tentativa de ataque. O dashboard Identidade e Acesso apresenta uma descrição geral do seu estado de identidade, incluindo:

* Número de tentativas falhadas de início de sessão. 
* As contas dos utilizadores que foram utilizadas durante essas tentativas.
* As contas que foram bloqueadas.
* As contas com palavras-passe alteradas ou repostas. 
* O número atual de contas com sessão iniciada.

## <a name="monitor-identity-and-access-activities"></a>Monitorizar atividades de identidade e acesso
Para ver as atividades atuais relacionadas com identidade e acesso, tem de aceder ao dashboard **Identidade e Acesso**.

1. Abra o dashboard **Centro de Segurança**.

2. No painel do lado esquerdo, em **Prevenção**, selecione **Identidade e Acesso**. Se tiver várias áreas de trabalho, é apresentado o seletor de área de trabalho.

    ![Seleção de área de trabalho](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > Se a coluna mais à direita mostrar **ATUALIZAR PLANO**, esta área de trabalho está a utilizar a subscrição gratuita. Atualize para a subscrição Standard para utilizar esta funcionalidade. Se a coluna mais à direita mostrar **PRECISA DE ATUALIZAÇÃO**, atualize o [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para utilizar esta funcionalidade. Para obter mais informações sobre o plano de preços, leia o artigo sobre os preços do Centro de Segurança. 
    > 
3. Se tiver mais do que uma área de trabalho para investigar, pode priorizar a investigação de acordo com a coluna **INÍCIOS DE SESSÃO FALHADOS**. Mostra o número atual de tentativas de início de sessão sem êxito nesta área de trabalho. Selecione a área de trabalho que pretende utilizar e o dashboard **Identidade e Acesso** é apresentado.

    ![Identidade e Acesso](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. As informações disponíveis neste dashboard podem ajudá-lo a identificar imediatamente uma potencial atividade suspeita. O dashboard está dividido em três áreas principais:

    a. **Postura de identidade**. Resume as atividades relacionadas com identidades que ocorrem nesta área de trabalho.

    b. **Inícios de sessão falhados**. Ajuda a identificar rapidamente a causa principal das tentativas de início de sessão falhadas. Mostra uma lista das 10 contas principais que tiveram o maior número de tentativas falhadas de início de sessão.

    c. **Inícios de sessão ao longo do tempo**. Ajuda a identificar rapidamente o número de inícios de sessão ao longo do tempo. Mostra uma lista de tentativas de início de sessão da conta principal do computador.
    
Independentemente do mosaico que selecionar, o dashboard que é apresentado baseia-se na consulta de Pesquisa de Registos. A única diferença é o tipo de consulta e o resultado. Pode ainda selecionar um item, como, por exemplo, um computador e ver os dados relevantes. 

## <a name="see-also"></a>Consultar também
Neste artigo, aprendeu a monitorizar a identidade e o acesso no Centro de Segurança. Para saber mais sobre o Centro de Segurança, veja os artigos seguintes:

* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerir alertas e responder a incidentes de segurança no Centro de Segurança.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Compreender os alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de resolução de problemas do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como resolver problemas comuns no Centro de Segurança. 
* [Centro de Segurança do Azure FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md). Encontre respostas para as perguntas mais frequentes sobre o Centro de Segurança.
* [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.


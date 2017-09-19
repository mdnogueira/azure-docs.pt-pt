---
title: "Monitorizar a Identidade e o Acesso no Centro de Segurança do Azure | Microsoft Docs"
description: "Este documento ajuda-o a utilizar a capacidade de identidade e acesso no Centro de Segurança do Azure para monitorizar as atividades de acesso dos seus utilizadores e os problemas relacionados com identidades."
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
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 2e351cd38e7baefc09fa36ceabffec92de22433b
ms.contentlocale: pt-pt
ms.lasthandoff: 09/14/2017

---
# <a name="monitoring-identity-and-access-in-azure-security-center"></a>Monitorizar a Identidade e o Acesso no Centro de Segurança do Azure
Este documento ajuda-o a utilizar o Centro de Segurança do Azure para monitorizar as identidades dos utilizadores e as atividades de acessos.

## <a name="why-monitor-identity-and-access"></a>Porquê monitorizar a identidade e o acesso?
A identidade deve ser o plano de controlo da sua empresa e proteger a sua identidade deve ser a sua prioridade. Enquanto no passado existiam perímetros à volta das organizações e esses perímetros eram um dos limites de defesa primários, hoje em dia, com mais dados e mais aplicações a moverem-se para a nuvem, a identidade torna-se o novo perímetro.

Através da monitorização das atividades de identidade será capaz de levar a cabo ações proativas antes da ocorrência de um incidente ou ações reativas que param uma tentativa de ataque. O dashboard de Identidade e Acesso proporciona-lhe uma descrição geral do estado da identidade, incluindo o número de tentativas falhadas de início de sessão, as contas de utilizador que foram utilizadas durante essas tentativas, as contas que foram bloqueadas, as contas com palavras-passe alteradas ou repostas e o número atual de contas que têm sessão iniciada.

## <a name="how-to-monitor-identity-and-access-activities"></a>Como monitorizar a identidade e as atividades de acessos?
Siga os passos abaixo para ver as atividades atuais relacionadas com identidade e acesso, tem de aceder ao dashboard **Identidade e Acesso**:

1.  Abra o dashboard do **Centro de Segurança**.
2.  No painel do lado esquerdo, em **Prevenção**, clique em **Identidade e Acesso**. Se tiver várias áreas de trabalho, é apresentado o seletor de área de trabalho.

    ![seleção de área de trabalho](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > Se a última coluna mostrar **ATUALIZAR PLANO**, tal deve-se ao facto de a área de trabalho utilizar a subscrição gratuita e terá de atualizar para a versão standard para utilizar esta funcionalidade. Se mostrar REQUER ATUALIZAÇÃO, tem de atualizar para o [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para a utilizar. Para obter mais informações sobre o plano de preços, leia o artigo sobre os preços do Centro de Segurança do Azure. 
    > 
3. Se tiver mais de uma área de trabalho a investigar, pode priorizar a investigação de acordo com a coluna **INÍCIOS DE SESSÃO FALHADOS**, que mostra o número atual de tentativas de início de sessão falhadas nessa área de trabalho. Selecione a área de trabalho que pretende utilizar e o dashboard de **Identidade e Acesso** é apresentado.

    ![identidade e acesso](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. As informações disponíveis neste dashboard podem ajudá-lo a identificar imediatamente uma potencial atividade suspeita. Este dashboard está dividido em três áreas principais:
    * **Postura de identidade**: resume as atividades relacionadas com identidades que estão a ocorrer nesta área de trabalho.
    * **Inícios de sessão falhados**: ajuda-o a identificar rapidamente a causa principal da tentativa de início de sessão falhada e mostra uma lista das dez contas cujas tentativas de início de sessão mais falharam.
    * **Inícios de sessão ao longo do tempo**: ajuda-o a identificar rapidamente a quantidade de inícios de sessão ao longo do tempo e mostra uma lista das principais tentativas de início de sessão de contas de computador.
    
Independentemente do mosaico que selecionar, o dashboard que vai ser apresentado baseia-se na [consulta da Pesquisa de Registos](https://docs.microsoft.com/azure/security-center/security-center-search), sendo que a única diferença é o tipo de consulta e o resultado. Pode ainda selecionar um item, como, por exemplo, um computador; clique no mesmo e veja os dados relevantes. 

## <a name="see-also"></a>Consultar também
Neste documento, aprendeu a monitorizar a identidade e o acesso no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerir alertas e responder a incidentes de segurança no Centro de Segurança.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Compreender os alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de Resolução de Problemas do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como resolver problemas comuns no Centro de Segurança. 
* [Centro de Segurança do Azure FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md). Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.



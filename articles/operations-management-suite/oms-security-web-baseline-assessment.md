---
title: "Avaliação da Linha de Base Web na Linha de Base da Solução de Segurança e Auditoria do Operations Management Suite | Microsoft Docs"
description: "Este documento explica como utilizar a avaliação da linha de base Web da solução de Segurança e Auditoria do OMS a fim de realizar uma avaliação da linha de base de todos os servidores Web monitorizados para fins de segurança e de conformidade."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: yurid
ms.openlocfilehash: 40b0c6ca933ea02ac9f5fe3bfaaf87a310542a8d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="web-baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Avaliação da Linha de Base Web em Solução de Segurança e Auditoria do Operations Management Suite
Este documento ajuda-o a utilizar as capacidades de avaliação da linha de base Web da solução de Segurança e Auditoria do OMS para aceder ao estado seguro dos seus recursos monitorizados.

## <a name="what-is-web-baseline-assessment"></a>O que é a avaliação da linha de base Web?
Atualmente, a Segurança OMS fornece avaliação de linha de base de segurança para sistemas operativos. Esta solução analisa as definições de SO dos servidores a cada 24 horas e oferece uma perspetiva das definições potencialmente vulneráveis. Leia [Avaliação da Linha de Base em Solução de Segurança e Auditoria do Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/oms-security-baseline) para obter mais informações sobre este tema.

A Avaliação da Linha de Base Web tem por objetivo localizar definições de servidor Web potencialmente vulneráveis. As três origens primárias para as configurações de linha de base Web são: configuração de .NET, ASP.NET e IIS.  Tal como a avaliação de linha de base do sistema operativo, a Segurança OMS vai analisar os servidores Web cada 24 horas e fornecer uma vista de estado de segurança dos mesmos.  No Serviço de Informação Internet (IIS), as configurações são altamente personalizáveis, o que permite a substituição de vários níveis de aplicação e site. O scanner verifica as definições em cada nível de aplicação/site para além do nível de raiz predefinido. Isto ajuda a identificar definições potencialmente vulneráveis e a corrigi-las sem demora, além de fornecer as nossas recomendações para essas definições.

>[!NOTE] 
>Pode transferir os Identificadores de Configuração Comuns e as Regras de Linha de Base utilizados pela Segurança do OMS nesta [página](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335?redir=0).


## <a name="web-security-baseline-assessment"></a>Avaliação da linha de base de segurança Web

Para esta pré-visualização, a funcionalidade pode ser acedida através da opção Pesquisa do OMS e do Dashboard Segurança e Auditoria do OMS. Siga os passos abaixo para realizar a consulta adequada:

1. No dashboard principal do **Microsoft Operations Management Suite**, clique em mosaico de **Segurança e Auditoria**.
2. No dashboard **Segurança e Auditoria**, pode ver a perspetiva da Linha de Base Web junto da perspetiva da linha de base do SO.
   
    ![Avaliação da Linha de Base de Segurança Web de Segurança e Auditoria do OMS](./media/oms-security-web-baseline/oms-security-web-baseline-fig5.png)

3. O painel esquerdo mostra o número de Servidores Web em comparação com a linha de base, a percentagem média de regras transmitidas em todos os servidores avaliados e a lista de Servidores sujeitos a avaliação.
4. O painel direito lista as regras exclusivas em que ocorreram falhas por *Gravidade* e *Tipo de Regra*. Se clicar em qualquer uma das regras do painel direito, serão mostrados os detalhes dessa regra. Segue-se um exemplo na imagem abaixo. A regra avaliada aparece listada em *Definição da Regra*. O campo *AzId* é um identificador exclusivo para cada regra utilizada pela Microsoft para controlar as regras de linha de base. Além disso, os utilizadores podem ver o *Resultado Esperado* (o valor recomendado da Microsoft) e outros detalhes relacionados com o impacto da regra na segurança.
    
    ![Consulta](./media/oms-security-web-baseline/oms-security-web-baseline-fig6.png)

5. Pode criar as suas próprias consultas para analisar os resultados. 

A primeira consulta que pode utilizar é o **Resumo de Avaliação da Linha de Base Web**. No campo **Iniciar pesquisa aqui**, escreva esta consulta: *Type=SecurityBaselineSummary BaselineType=Web*. Segue-se um exemplo da saída de dados:

![Resultado da Consulta](./media/oms-security-web-baseline/oms-security-web-baseline-fig7.png)

>[!NOTE] 
>Nesta consulta, cada registo indica o resumo de avaliação num único servidor.

Assim que estiver na **Pesquisa de Registo**, pode escrever consultas diferentes para obter mais informações sobre a avaliação de linha de base Web. Para além da consulta anterior, também pode utilizar as seguintes nesta pré-visualização:

**Avaliação da Regra de Linha de Base Web**: cada registo representa uma avaliação da regra de linha de base web única num único servidor. Inclui todos os dados de uma regra com falhas, o *Caminho do Site* no qual a regra foi avaliada, o *Resultado Esperado* e o *Resultado Real*.

Consulta: *Type=SecurityBaseline BaselineType=Web AnalyzeResult=Failed*

![Resultado da Consulta 2](./media/oms-security-web-baseline/oms-security-web-baseline-fig8.png)

**Mostrar todos os resultados de um servidor específico**: esta consulta mostra como ver os resultados de um servidor específico. Consulta: *Type=SecurityBaseline BaselineType=Web Computer=BaselineTestVM1*

![Resultado da Consulta 3](./media/oms-security-web-baseline/oms-security-web-baseline-fig3.png)

Pode utilizar estes registos/consultas para criar os seus próprios dashboards, relatórios ou alertas. Segue-se um controlo de IU de exemplo que pode adicionar ao dashboard. Pode aprender como visualizar os dados utilizando o Estruturador de Vistas OMS [aqui](https://blogs.technet.microsoft.com/msoms/2016/06/30/oms-view-designer-visualize-your-data-your-way/). O ecrã abaixo apresenta um exemplo do aspeto do mosaico após efetuar esta personalização.

![dashboard](./media/oms-security-web-baseline/oms-security-web-baseline-fig4.png)

## <a name="see-also"></a>Consultar também
Neste documento, ficou a conhecer a avaliação da Linha de Base Web de Segurança e Auditoria do OMS. Para saber mais sobre a Segurança do OMS, veja os artigos seguintes:

* [Descrição geral do Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Monitorização e Resposta aos Alertas de Segurança na Solução de Segurança e Auditoria do Operations Management Suite](oms-security-responding-alerts.md)
* [Recursos de Monitorização na Solução de Segurança e Auditoria do Operations Management Suite](oms-security-monitoring-resources.md)


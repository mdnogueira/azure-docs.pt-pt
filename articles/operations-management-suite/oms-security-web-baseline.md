---
title: "Linha de Base Web de Solução de Segurança e Auditoria do Operations Management Suite | Microsoft Docs"
description: "Este documento explica como utilizar a Segurança OMS e a solução de Auditoria para efetuar uma avaliação de linha de base Web de todos os servidores Web monitorizados para o objetivo de segurança e de conformidade."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ROBOTS: NOINDEX
redirect_url: https://www.microsoft.com/cloud-platform/security-and-compliance
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 0d4707dc0c0ffbf40d0d10a6d12b9709a9655258
ms.contentlocale: pt-pt
ms.lasthandoff: 05/03/2017


---
# <a name="web-baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Avaliação da Linha de Base Web em Solução de Segurança e Auditoria do Operations Management Suite
Este documento ajuda-o a utilizar as capacidades de avaliação da linha de base Web da [Solução de Segurança e Auditoria do Operations Management Suite (OMS)](operations-management-suite-overview.md) para aceder ao estado seguro dos seus recursos monitorizados.

## <a name="what-is-web-baseline-assessment"></a>O que é a Avaliação da Linha de Base Web?
Atualmente, a Segurança OMS fornece avaliação de linha de base de segurança para sistemas operativos. Este analisa as definições de sistema operativo dos servidores a cada 24 horas e fornece uma vista para as definições de potencialmente vulneráveis. Leia [Avaliação da Linha de Base em Solução de Segurança e Auditoria do Operations Management Suite](oms-security-baseline.md) para obter mais informações sobre este tema.

O objetivo da avaliação de linha de base Web é localizar as definições de servidor Web potencialmente vulnerável. As três origens primárias para as configurações de linha de base Web são: configuração de .NET, ASP.NET e IIS.  Tal como a avaliação de linha de base do sistema operativo, a Segurança OMS vai analisar os servidores Web cada 24 horas e fornecer uma vista de estado de segurança dos mesmos.  No Serviço de Informação Internet (IIS), as configurações são altamente personalizáveis, o que permite a substituição de vários níveis de aplicação e site. O scanner verifica as definições em cada nível de aplicação/site para além do nível de raiz predefinido. Isto ajuda a identificar potenciais localizações de definições de vulnerabilidade e remediar rapidamente.


## <a name="web-security-baseline-assessment"></a>Avaliação da Linha de Base de Segurança Web
Para esta pré-visualização, esta funcionalidade será acedida durante a opção de Pesquisa OMS. Siga os passos abaixo para realizar a consulta adequada:

1. No dashboard principal do **Microsoft Operations Management Suite** , clique em mosaico de **Segurança e Auditoria**.
2. No dashboard **Segurança e Auditoria**, clique no botão **Pesquisa de Registo**.
3. A primeira consulta que pode utilizar é o **Resumo de Avaliação da Linha de Base Web**. No campo **Iniciar pesquisa aqui**, escreva esta consulta: tipo*=SecurityBaselineSummary BaselineType=web*. O ecrã seguinte apresenta uma amostra de saída:

![Resumo de avaliação da linha de base Web](./media/oms-security-web-baseline/oms-security-web-baseline-fig1-new.png)

> [!NOTE]
> Nesta consulta, cada registo indica o resumo de avaliação num único servidor.

Assim que estiver na **Pesquisa de Registo**, pode escrever consultas diferentes para obter mais informações sobre a avaliação de linha de base Web. Para além da consulta anterior, também pode utilizar os seguintes nesta pré-visualização.

**Avaliação da Regra de Linha de Base Web**: cada registo representa uma avaliação da regra de linha de base web única num único servidor. Inclui todos os dados para a regra, localização, o resultado esperado e o resultado real.

**Consulta**: tipo*=SecurityBaseline BaselineType=web*

![Avaliação da regra da linha de base Web](./media/oms-security-web-baseline/oms-security-web-baseline-fig2.png)

**Mostrar todos os resultados para um servidor específico**: esta consulta mostra como ver resultados de um servidor específico.

**Consulta**: *Type=SecurityBaseline BaselineType=web Computer=BaselineTestVM1*

![Todos os resultados](./media/oms-security-web-baseline/oms-security-web-baseline-fig3.png)

Também pode utilizar estes registos/consultas para criar os seus dashboards, relatórios ou alertas. O ecrã abaixo tem um controlo de IU de exemplo que pode adicionar ao dashboard. Pode aprender como visualizar os dados utilizando o Estruturador de Vistas OMS [aqui](https://blogs.technet.microsoft.com/msoms/2016/06/30/oms-view-designer-visualize-your-data-your-way/). O ecrã abaixo apresenta um exemplo do aspeto do mosaico após efetuar esta personalização.

![IU de Exemplo](./media/oms-security-web-baseline/oms-security-web-baseline-fig4.png)

> [!NOTE]
> Se gostaria de saber as definições que são verificadas relativamente à avaliação de linha de base, pode transferir [esta folha de cálculo do Excel](https://gallery.technet.microsoft.com/OMS-Web-Baseline-1e811690) que contém tais definições.

## <a name="see-also"></a>Consultar também
Neste documento, aprendeu sobre a avaliação da linha de base Web de Segurança e Auditoria do OMS. Para saber mais sobre a Segurança do OMS, veja os artigos seguintes:

* [Descrição geral do Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Monitorização e Resposta aos Alertas de Segurança na Solução de Segurança e Auditoria do Operations Management Suite](oms-security-responding-alerts.md)
* [Recursos de Monitorização na Solução de Segurança e Auditoria do Operations Management Suite](oms-security-monitoring-resources.md)



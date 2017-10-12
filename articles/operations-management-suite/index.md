---
title: "Documentação do Operations Management Suite (OMS) do Azure - Tutoriais | Microsoft Docs"
description: "O Microsoft Operations Management Suite (OMS) é a solução de gestão de TI baseada na nuvem da Microsoft que o ajuda a gerir e a proteger a sua infraestrutura no local e na nuvem. Este artigo identifica os diferentes serviços incluídos no OMS e fornece ligações para o respetivo conteúdo detalhado."
services: operations-management-suite
author: czeumault
manager: carolz
layout: LandingPage
ms.assetid: 
ms.service: operations-management-suite
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 01/23/2017
ms.author: carolz
ms.openlocfilehash: 12f959376d4923e4e2481e37108ade632ac14902
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-operations-management-suite-oms"></a>O que é o Operations Management Suite (OMS)?
O Microsoft Operations Management Suite (OMS) é a solução de gestão de TI baseada na nuvem da Microsoft que o ajuda a gerir e a proteger a sua infraestrutura no local e na nuvem.  Quando o OMS estiver implementado como um serviço baseado na nuvem, pode colocá-lo em execução de imediato com o mínimo investimento em termos de serviços de infraestrutura.  As novas funcionalidades são entregues automaticamente, reduzindo os seus custos de manutenção e atualização.

Além de fornecer serviços importantes, o OMS pode ser integrado com componentes do System Center, como o System Center Operations Manager para expandir os seus investimentos de gestão existente para a nuvem.  O System Center e o OMS podem trabalhar em conjunto para fornecer uma experiência de gestão híbrida completa.

As secções seguintes fornecem uma descrição de alto nível das diferentes áreas de valor do OMS e dos serviços que os implementarem.  Pode ver a arquitetura de OMS para obter uma descrição geral dos diferentes componentes de OMS antes de rever a documentação detalhada de cada um.

## <a name="insight-and-analyticsmediaoperations-management-suite-overviewicon-insight-analyticspng-insight-and-analytics"></a>![Conhecimentos aprofundados e análise](media/operations-management-suite-overview/icon-insight-analytics.png) Conhecimentos aprofundados e análise
O [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) ajudam-no a recolher, correlacionar, pesquisar e atuar em relação aos dados de registo e desempenho gerados por sistemas operativos e aplicações. Fornece-lhe conhecimentos aprofundados operacionais em tempo real, ao utilizar pesquisa integrada e dashboards personalizados para analisar de imediato milhões de registos em todas as todas as suas cargas de trabalho e servidores, independentemente da respetiva localização física.

Pode adicionar facilmente soluções ao Log Analytics que define os dados que vão ser recolhidos e a lógica para a respetiva análise.  As soluções podem incluir funcionalidades adicionais que são entregues automaticamente a agentes com uma configuração mínima ou sem configuração.  Além de utilizar as ferramentas de análise fornecidas por soluções individuais, pode efetuar pesquisas personalizadas em todo o conjunto de dados para correlacionar dados entre sistemas e aplicações.  

## <a name="automation--controlmediaoperations-management-suite-overviewicon-automation-controlpng-automation--control"></a>![Automatização e controlo](media/operations-management-suite-overview/icon-automation-control.png) Automatização e controlo
A Automatização do Azure automatiza processos administrativos com [runbooks](../automation/automation-runbook-types.md) que são baseados no PowerShell e executados na nuvem do Azure.  Os runbooks podem aceder a qualquer produto ou serviço que pode ser gerido com o PowerShell, incluindo recursos noutras nuvens, tais como serviços do Amazon Web (AWS).  Os runbooks podem ser também executados no seu centro de dados local para gerir recursos locais.

A Automatização do Azure fornece gestão de configuração com o [PowerShell DSC](../automation/automation-dsc-overview.md).  Pode criar e gerir recursos de DSC alojados no Azure e aplicá-los em sistemas de nuvem e no local para definir e aplicar automaticamente a respetiva configuração.

## <a name="protection-and-recoverymediaoperations-management-suite-overviewicon-protection-recoverypng-protection-and-disaster-recovery"></a>![Proteção e recuperação](media/operations-management-suite-overview/icon-protection-recovery.png) Proteção e recuperação após desastres
O [Azure Backup](http://azure.microsoft.com/documentation/services/backup) protege os dados de aplicação e mantém-nos durante anos sem qualquer investimento de capital e com um mínimo de custos operacionais.  Pode criar cópias de segurança de dados a partir de servidores Windows físicos e virtuais, além de cargas de trabalho de aplicações como o SQL Server e o SharePoint.  Também pode ser utilizado pelo System Center Data Protection Manager (DPM) para replicar dados protegidos para o Azure para redundância e armazenamento de longa duração.

O [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) contribui para a estratégia da continuidade do seu negócio e recuperação após desastre (BCDR) ao programar a replicação, ativação pós-falha e recuperação de máquinas virtuais de Hyper-V no local, máquinas virtuais VMware e servidores Windows/Linux físicos. Pode replicar máquinas para um centro de dados secundária ou expandir o seu centro de dados replicando-os para o Azure. O Site Recovery também fornece ativação pós-falha e recuperação simples para cargas de trabalho. Integra-se com mecanismos de recuperação após desastres, como o SQL Server AlwaysOn e fornece planos de recuperação para ativação pós-falha fácil de cargas de trabalho que estão em camadas em várias máquinas.

## <a name="oms-security-and-compliancemediaoperations-management-suite-overviewicon-security-compliancepng-security-and-compliance"></a>![Segurança e conformidade de OMS](media/operations-management-suite-overview/icon-security-compliance.png) Segurança e Conformidade
Segurança e Conformidade ajuda-o a identificar, avaliar e mitigar os riscos de segurança para a sua infraestrutura.  Estas funcionalidades do OMS estão implementadas através de várias soluções no Log Analytics que analisam dados de registo e configuração a partir de sistemas de agentes para o ajudar a garantir a segurança contínua do seu ambiente.

* A [solução de Segurança e Auditoria](oms-security-getting-started.md) recolhe e analisa os eventos de segurança nos sistemas geridos para identificar atividade suspeita.
* A [solução Software maligno](../log-analytics/log-analytics-malware.md) comunica o estado de proteção de software maligno em sistemas geridos.  
* A solução Atualizações do Sistema efetua uma análise das atualizações de segurança e outras atualizações nos sistemas geridos, para que possa identificar facilmente sistemas que requerem a aplicação de patches.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics).
* Saiba mais sobre o [Automatização do Azure](../automation/automation-intro.md).
* Saiba mais sobre o [Azure Backup](http://azure.microsoft.com/documentation/services/backup).
* Saiba mais sobre o [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery).


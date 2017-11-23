---
title: "Monitorização no Microsoft Azure | Microsoft Docs"
description: "Opções quando pretende monitorizar nada no Microsoft Azure. Monitor do Azure, Application Insights e análise de registos"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: robb
ms.openlocfilehash: c34211e0c55c10defaa32f1e0a2195514ff3ae5f
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="overview-of-monitoring-in-microsoft-azure"></a>Descrição geral de monitorização no Microsoft Azure
Este artigo fornece uma descrição geral das ferramentas e serviços envolvidos na forma holística monitorização do Microsoft Azure. Aplica-se a:
- Utilizar serviços do Azure para monitorizar a infraestrutura do Azure e aplicações
- Utilizando os serviços do Azure para híbrida monitor e infraestrutura não do Azure e aplicações
- Utilizando os serviços de não do Azure para monitorizar a infraestrutura do Azure e aplicações

Este artigo aborda os vários produtos e serviços disponíveis e como funcionam em conjunto. -Pode ajudá-lo a determinar qual ferramentas são mais adequadas para si que casos.  

## <a name="why-use-azures-monitoring-services"></a>Porquê utilizar serviços de monitorização do Azure?

Problemas de desempenho na sua aplicação em nuvem podem afetar a sua empresa. Com os vários componentes interligados e versões frequentes, degradations pode acontecer em qualquer altura. E se estiver a desenvolver uma aplicação, os utilizadores, geralmente, detetar problemas que não foi encontrado no teste. Deve conhecer sobre estes problemas imediatamente e tem as ferramentas para diagnosticar e corrigir os problemas. Além disso, os problemas na sua aplicação podem resultar da infraestrutura subjacente em que as aplicações executam, pelo que ter uma vista holística da sua aplicação e a infraestrutura de chave para monitorização do seu ambiente do Azure. Microsoft Azure tem uma série de ferramentas para identificar e resolver esses problemas.

## <a name="how-do-i-monitor-my-azure-environment"></a>Como monitorizar o meu ambiente do Azure?

Há uma série de ferramentas para monitorização do seu ambiente do Azure, do código da aplicação em execução no Azure para os serviços e a infraestrutura de alojamento da sua aplicação. Estas ferramentas funcionam em conjunto para oferecer a monitorização de nuvem abrangente e incluem:

-   **Monitor do Azure** -o serviço do Azure que funciona como um pipeline consolidado para todos os dados de monitorização de serviços do Azure. Fornece acesso a eventos que descrevem o funcionamento da infraestrutura do Azure e os serviços do Azure que está a utilizar e métricas de desempenho. Monitor do Azure é um pipeline de dados de monitorização para o seu ambiente do Azure e oferece os dados diretamente para a análise de registos, bem como as ferramentas de terceiros 3rd onde pode obter conhecimentos aprofundados sobre os dados e combiná-lo com dados de no local ou outros recursos de nuvem.

-   **Application Insights** -o serviço do Azure que oferece a análise de utilizador e de monitorização do desempenho de aplicação. Monitoriza o código que escrevemos e aplicações implementadas no Azure, no local ou outras nuvens. Ao instrumentar a sua aplicação com o Application Insights SDK pode obter acesso a uma variedade de dados, incluindo os tempos de resposta de dependências, rastreios de exceção, os instantâneos de depuração e perfis de execução. Fornece ferramentas poderosas para analisar a telemetria de aplicação ao desenvolver e a funcionar a sua aplicação. Profundamente integrado com o Visual Studio para que possa obter certa para as linhas de problema de código que pode corrigir este e oferece a análise de utilização para analisar a utilização de cliente das suas aplicações para os gestores de produto, bem como.

-   **Análise de registo** -anteriormente conhecida como a análise de registos do OMS, é um serviço Azure que ingere dados de registo e a métrica de serviços do Azure (através do Azure Monitor), as VMs do Azure e no local ou outra infraestrutura de nuvem e oferece a pesquisa de registo flexível e out-os análise de caixa sobre estes dados. Fornece ferramentas avançadas para analisar os dados entre origens, permite consultas complexas em todos os registos e proativamente podem alertá em condições especificadas.  Mesmo pode recolher dados personalizados para o repositório central, para que possa consultar e visualizá-lo. Também pode tirar partido das soluções de incorporadas do registo análise obtenha imediatamente informações sobre a segurança e a funcionalidade da sua infraestrutura.

## <a name="accessing-monitoring-in-the-azure-portal"></a>Aceder a monitorização no portal do Azure
Todos os serviços de monitorização do Azure estão agora disponíveis num painel único de IU. Para obter mais informações sobre como aceder a esta área, consulte [introdução ao Azure Monitor](monitoring-get-started.md). 

Também pode aceder a funções de monitorização de recursos do Azure específicos, realçando esses recursos e desagregar as opções de monitorização. 

## <a name="examples-of-when-to-use-which-tool"></a>Exemplos de quando deve utilizar qual ferramenta 

As secções seguintes mostram alguns cenários básicos e ferramentas de que devem ser utilizadas em conjunto. 

### <a name="scenario-1--fix-errors-in-an-azure-application-under-development"></a>Cenário 1 – corrija erros numa aplicação do Azure em desenvolvimento   

**A melhor opção é utilizar o Application Insights, o Monitor do Azure e o Visual Studio em conjunto**

O Azure oferece agora o poder do depurador do Visual Studio na nuvem. Configure a monitorização do Azure para enviar telemetria para o Application Insights. Ative o Visual Studio para incluir o Application Insights SDK na sua aplicação. Uma vez no Application Insights, pode utilizar o mapa de aplicação para detetar visualmente estão em bom estadas as partes da sua aplicação em execução ou não. Para as peças que não estão em bom estadas, erros e exceções já estão disponíveis para exploração. Pode utilizar a análise de vários no Application Insights para aceder mais aprofundado. Se não tiver a certeza sobre o erro, pode utilizar o depurador do Visual Studio para rastrear no ponto de código e o pin ainda mais um problema. 

Para obter mais informações, consulte [monitorização Web Apps](../application-insights/app-insights-azure-web-apps.md) e consultar a tabela de conteúdo à esquerda para obter instruções em vários tipos de aplicações e idiomas.  

### <a name="scenario-2--debug-an-azure-net-web-application-for-errors-that-only-show-in-production"></a>Cenário 2 – depurar uma aplicação web do .NET do Azure para erros que mostram apenas em produção 

> [!NOTE]
> Estas funcionalidades estão na pré-visualização. 

**A melhor opção é utilizar o Application Insights e que o se possíveis Visual Studio para a depuração completa.**

Utilize o depurador de instantâneo do Application Insights para depurar a aplicação. Se um determinado limiar de erro ocorre com componentes de produção, o sistema captura automaticamente telemetria no windows de tempo chamado "instantâneos." A quantidade capturada é segura para uma nuvem de produção porque é suficientemente pequena para não afetar o desempenho, mas significativo permitir que o rastreio.  O sistema pode capturar vários instantâneos. Pode observar um ponto no tempo no portal do Azure ou utilizar o Visual Studio da experiência completa. Com o Visual Studio, os programadores podem percorrer nesse instantâneo, como se foram de depuração em tempo real. As variáveis locais, os parâmetros, memória e frames estão disponíveis. Os programadores tem de ser concedidos acesso a estes dados de produção através de um [função RBAC](../active-directory/role-based-access-built-in-roles.md).  

Para obter mais informações, consulte [instantâneo depuração](../application-insights/app-insights-snapshot-debugger.md). 

### <a name="scenario-3--debug-an-azure-application-that-uses-containers-or-microservices"></a>Cenário 3 – depurar uma aplicação do Azure que utiliza contentores ou micro-serviços 

**Mesmo que o cenário 1. Utilizar o Application Insights, o Monitor do Azure e o Visual Studio em conjunto**

O Application Insights suporta também a recolha de telemetria a partir de processos em execução no interior de contentores e micro-serviços (Kubernetes Docker, Azure Service Fabric). Para obter mais informações, [veja este vídeo sobre a depuração de contentores e micro-serviços](https://go.microsoft.com/fwlink/?linkid=848184). 


### <a name="scenario-4--fix-performance-issues-in-your-azure-application"></a>Problemas de desempenho de correção de cenário 4 – na sua aplicação do Azure

O [gerador de perfis do Application Insights](../application-insights/app-insights-profiler.md) foi concebido para ajudar a resolver estes tipos de problemas. Pode identificar e resolver problemas de desempenho para aplicações em execução nos serviços de aplicações (aplicações Web, as Logic Apps, Mobile Apps, API Apps, aplicações de função) e outros recursos, tais como máquinas virtuais, conjuntos de dimensionamento de Máquina Virtual (VMSS), serviços em nuvem, de computação e Recursos de infraestrutura de serviço. 

> [!NOTE]
> Capacidade de máquinas virtuais, máquinas virtuais conjuntos de dimensionamento (VMSS), serviços em nuvem e de recursos de infraestrutura de serviços do perfil está em pré-visualização.   

Além disso, será proativamente notificado por e-mail sobre determinados tipos de erros, tais como os tempos de carregamento de páginas lento, pela ferramenta de deteção inteligente.  Não precisa de efetuar qualquer configuração nesta ferramenta. Para obter mais informações, consulte [deteção inteligente - anomalias de desempenho](../application-insights/app-insights-proactive-performance-diagnostics.md).



## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre

* [Monitor do Azure um vídeo de Ignite 2016](https://myignite.microsoft.com/videos/4977)
* [Introdução ao Monitor do Azure](monitoring-get-started.md)
* [Diagnóstico do Azure](../azure-diagnostics.md) se está a tentar diagnosticar problemas no seu serviço de nuvem, a Máquina Virtual, a Máquina Virtual Dimensionar aplicação de recursos de infraestrutura definida ou serviço.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) se estiver a tentar diagnóstico problemas na sua aplicação de serviço Web da aplicação.
* [Análise de registo](https://azure.microsoft.com/documentation/services/log-analytics/) e [Operations Management Suite](https://www.microsoft.com/oms/) produção solução de monitorização

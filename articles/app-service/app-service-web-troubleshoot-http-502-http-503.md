---
title: "Corrigir 502 gateway incorreto, 503 Serviço indisponíveis erros | Microsoft Docs"
description: "Resolver problemas de gateway incorreto 502 e 503 erros indisponível de serviço na sua aplicação web alojadas no App Service do Azure."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: top-support-issue
keywords: "502 gateway incorreto, 503 Serviço indisponível, erro 503, erros 502"
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.openlocfilehash: 397a6aaf7dc27adfa0fc0e722b8a2be5cc1d75f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-your-azure-web-apps"></a>Resolver erros HTTP de "502 incorreto gateway" e "503 Serviço indisponível" nas suas aplicações web do Azure
"gateway incorreto 502" e "503 Serviço indisponível" são erros comuns na aplicação web alojadas no [App Service do Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Este artigo ajuda-o a resolver estes erros.

Se precisar de mais ajuda, a qualquer altura neste artigo, pode contactar as especialistas do Azure no [do MSDN Azure e os fóruns Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode também ficheiro um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **obter suporte**.

## <a name="symptom"></a>Sintoma
Quando navega para a aplicação web, devolve uma HTTP Erro "502 incorreto Gateway" ou um HTTP "503 Serviço indisponível" erro.

## <a name="cause"></a>Causa
Este problema é frequentemente causado por problemas de nível de aplicação, tais como:

* pedidos a demorar muito tempo
* utilização de CPU/memória elevada de aplicação
* aplicação a falhar devido a uma exceção.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Passos de resolução de problemas para resolver erros de "503 Serviço indisponível" e "502 gateway incorretos"
Resolução de problemas pode ser dividida em três tarefas diferentes, por ordem sequencial:

1. [Observar e monitorizar o comportamento da aplicação](#observe)
2. [Recolher dados](#collect)
3. [Mitigar o problema](#mitigate)

[Web Apps do App Service](/services/app-service/web/) dá-lhe várias opções de cada passo.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Observar e monitorizar o comportamento da aplicação
#### <a name="track-service-health"></a>Controlar o estado de funcionamento do serviço
Microsoft Azure publicizes sempre que há uma degradação de interrupção ou de desempenho do serviço. Pode controlar o estado de funcionamento do serviço no [Portal do Azure](https://portal.azure.com/). Para obter mais informações, consulte [controlar o estado de funcionamento do serviço](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-web-app"></a>Monitorizar a aplicação web
Esta opção permite-lhe saber se a aplicação está a ter problemas. No painel da sua aplicação web, clique o **pedidos e erros** mosaico. O **métrica** painel irá mostrar todas as métricas, pode adicionar.

Alguns das métricas que pode querer monitorizar da sua aplicação web

* Memória média conjunto de trabalho
* Tempo de resposta médio
* Tempo de CPU
* Conjunto de trabalho de memória
* Pedidos

![monitorizar a aplicação web para resolver erros de HTTP 502 gateway incorreta e 503 service não está disponível](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Para obter mais informações, consulte:

* [Monitor Web Apps no App Service do Azure](web-sites-monitor.md)
* [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Recolher dados
#### <a name="use-the-azure-app-service-support-portal"></a>Utilizar o Portal de suporte do App Service do Azure
As Web Apps fornece-lhe a capacidade para resolver problemas relacionados com a sua aplicação web observando HTTP registos, registos de eventos, informações de estado do processo e muito mais. Pode aceder a todos os estas informações com o nosso portal de suporte em **http://&lt;o nome da aplicação >.scm.azurewebsites.net/Support**

O portal de suporte de serviço de aplicações do Azure fornece-lhe com três separadores separados para suportar os três passos de um cenário de resolução de problemas comuns:

1. Observar o comportamento atual
2. Analisar a recolher informações de diagnóstico e executando os analisadores incorporadas
3. Mitigar

Se o problema está a acontecer agora, clique em **analisar** > **diagnóstico** > **diagnosticar agora** para criar uma sessão de diagnóstico para si, que irá recolher registos de HTTP, os registos do Visualizador de eventos, memória, informações de estado, os registos de erros PHP e PHP processamento do relatório.

Depois dos dados são recolhidos, também irá executar uma análise nos dados e fornecer-lhe um relatório HTML.

No caso de pretender transferir os dados, por predefinição, teria de ser armazenado na pasta D:\home\data\DaaS.

Para obter mais informações sobre o portal de suporte de serviço de aplicações do Azure, consulte [novas atualizações a extensão de Site de suporte para Web sites do Azure](https://azure.microsoft.com/blog/new-updates-to-support-site-extension-for-azure-websites).

#### <a name="use-the-kudu-debug-console"></a>Utilize a consola de depuração do Kudu
As Web Apps vem com uma consola de depuração que pode utilizar para depuração, explorar, carregar ficheiros, bem como pontos finais JSON para obter informações sobre o ambiente. Esta opção é denominada a *consola Kudu* ou *SCM Dashboard* para a sua aplicação web.

Pode aceder a este dashboard acedendo à ligação **https://&lt;o nome da aplicação >.scm.azurewebsites.net/**.

Algumas das ações que o Kudu fornece são:

* definições de ambiente para a sua aplicação
* fluxo de registo
* informação de diagnóstico
* consola no qual pode executar cmdlets do Powershell e comandos DOS básicos de depuração.

Outra funcionalidade útil do Kudu é que, no caso da aplicação que está a gerar as exceções de primeira hipótese, pode utilizar o Kudu e informações de estado a ferramenta de SysInternals Procdump para criar a memória. Estas capturas de memória são instantâneos do processo e, muitas vezes, podem ajudar a resolver problemas mais complicados com a sua aplicação web.

Para obter mais informações sobre as funcionalidades disponíveis no Kudu, consulte [ferramentas online de Web sites do Azure que deve conhecer sobre](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Mitigar o problema
#### <a name="scale-the-web-app"></a>Dimensionar a aplicação web
No App Service do Azure, para um melhor desempenho e débito, pode ajustar a escala a que está a executar a aplicação. Como aumentar verticalmente a uma aplicação web envolve dois ações relacionadas: alterar o seu plano de serviço de aplicações para um escalão de preço superior e a configuração de determinadas definições depois de terem mudado para o escalão de preço superior.

Para obter mais informações sobre como aumentar, consulte [dimensionar uma aplicação web no App Service do Azure](web-sites-scale.md).

Além disso, pode optar por executar a sua aplicação em mais do que uma instância. Isto não só fornece-lhe mais capacidade de processamento, mas também lhe dê algum período de tolerância a falhas. Se o processo de ficar inativo numa instância, a outra instância irá continuar processar pedidos.

Pode definir o dimensionamento para ser Manual ou automático.

#### <a name="use-autoheal"></a>Utilizar AutoHeal
AutoHeal recicla o processo de trabalho para a sua aplicação com base nas definições que escolha (por exemplo, as alterações de configuração, pedidos, limites de memória ou o tempo necessário para executar um pedido). Na maioria das vezes, a Reciclagem do processo é a forma mais rápida para recuperar a partir de um problema. Embora sempre pode reiniciar a aplicação web de diretamente no Portal do Azure, AutoHeal fazê-lo automaticamente para si. Tudo o que precisa de fazer é adicionar alguns acionadores o Web. config de raiz para a sua aplicação web. Tenha em atenção que estas definições funciona da mesma forma, mesmo se a aplicação não é um .net um.

Para obter mais informações, consulte [Web Sites Azure Autorrecuperação](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-web-app"></a>Reinicie a aplicação web
Isto é, frequentemente, a forma mais simples para recuperar de problemas isolados. No [Portal do Azure](https://portal.azure.com/), no painel da sua aplicação web, tem as opções para interromper ou reiniciar a sua aplicação.

 ![reiniciar a aplicação para resolver erros de HTTP 502 gateway incorreta e 503 service não está disponível](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Também pode gerir a sua aplicação web com o Azure Powershell. Para obter mais informações, veja [Using Azure PowerShell with Azure Resource Manager (Utilizar o Azure PowerShell com o Azure Resource Manager)](../powershell-azure-resource-manager.md).


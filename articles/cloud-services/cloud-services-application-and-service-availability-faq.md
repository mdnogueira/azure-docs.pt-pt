---
title: "Aplicação e problemas de disponibilidade do serviço para o Microsoft Azure Cloud Services FAQ | Microsoft Docs"
description: "Este artigo apresenta uma lista de perguntas mais frequentes sobre a aplicação e a disponibilidade do serviço para serviços de nuvem do Microsoft Azure."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 663a89ca5b3cfdafd873b27bae6ae8d158ac976a
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Aplicação e problemas de disponibilidade do serviço de Cloud Services do Azure: Perguntas mais frequentes sobre (FAQ)

Este artigo inclui perguntas mais frequentes sobre a aplicação e problemas de disponibilidade do serviço para [dos serviços de nuvem do Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Também pode consultar o [página de tamanho de VM de serviços em nuvem](cloud-services-sizes-specs.md) para obter informações de tamanho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>A minha função foi reciclada. Houve qualquer atualização implementada para o serviço em nuvem?
Aproximadamente uma vez por mês, Microsoft disponibiliza uma nova versão de SO convidado para as VMs do Windows Azure PaaS. O SO convidado é apenas um desse atualização no pipeline. Uma versão pode ser afetada por vários outros fatores. Além disso, o Azure é executado em centenas de milhares de máquinas. Por conseguinte, não é possível prever a data e hora exatas quando as funções serão reiniciado. Atualizamos convidado SO atualizar Feed RSS com as informações mais recentes que temos, mas deve considerar que comunicou que o tempo para ser um valor aproximado. Iremos sabem o que se trata problemático para os clientes e a trabalhar para um plano de limite ou precisamente reinícios de tempo.

Para obter detalhes completos sobre atualizações de SO convidado recentes, consulte [versões de SO convidado do Azure e matriz de compatibilidade SDK](cloud-services-guestos-update-matrix.md).

Para obter informações úteis sobre reinicia e ponteiros para detalhes técnicos de atualizações do convidado e sistema operativo anfitrião, consulte a mensagem de blogue do MSDN [função instância reinicia devido a atualizações do SO](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Por que motivo o primeiro pedido efetuado à minha serviço em nuvem depois do serviço está inativo há algum tempo demoram mais tempo do que o habitual?
Quando o servidor Web recebe o primeiro pedido, primeiro recompiles o código e, em seguida, processa o pedido. É por esse motivo o primeiro pedido demora mais tempo do que os outros. Por predefinição, o conjunto de aplicação obtém encerrado em casos de inatividade do utilizador. O conjunto aplicacional reciclará também por predefinição em minutos 1,740 (29 horas).

Falha da aplicação de serviços de informação Internet (IIS) agrupamentos podem ser periodicamente reciclados para evitar instável Estados que podem levar a aplicação, bloqueia, ou fugas de memória.

Os documentos seguintes irão ajudá-lo a compreender e a mitigar este problema:
* [Corrigir lenta carga inicial para o IIS](http://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [IIS 7.5 web aplicação primeiro pedido após a Reciclagem do conjunto aplicacional é muito lento](http://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Se pretender alterar o comportamento predefinido do IIS, terá de utilizar tarefas de arranque, porque se aplicar manualmente as alterações para as instâncias de função da Web, as alterações, eventualmente, serão perdidas.

Para obter mais informações, consulte [como configurar e executar tarefas de arranque para um serviço em nuvem](cloud-services-startup-tasks.md).

---
title: "Guiam de Suportabilidade e a política de extinção de SO convidado do Azure | Microsoft Docs"
description: "Fornece informações sobre o que irá suporte da Microsoft como regards para o SO convidado do Azure utilizada pelos serviços de nuvem."
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: 
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: 4bc2d57cf4c7d6e0981aa1a5c7c989860600b897
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Política do Azure de Suportabilidade e extinção de SO convidado
As informações nesta página está relacionada com o sistema operativo convidado do Azure ([SO convidado](cloud-services-guestos-update-matrix.md)) para serviços em nuvem worker e web funções (PaaS). Não é aplicável a máquinas virtuais (IaaS).

A Microsoft tem uma publicados [política de suporte para o SO convidado](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq). A página que se agora descreve como a política é implementada.

A política é

1. Microsoft irá suportar **, pelo menos, as mais recentes duas famílias de SO convidado**. Quando uma família de é extinguida, os clientes têm de 12 meses contar da data de extinção oficial para atualizar para uma família de SO convidado suportada mais recente.
2. Microsoft irá suportar **, pelo menos, duas versões mais recentes das famílias de SO convidado suportadas**.
3. Microsoft irá suportar **, pelo menos, as duas versões mais recentes do Azure SDK**. Quando for retirada uma versão do SDK, os clientes terão de 12 meses contar da data de extinção oficial para atualizar para uma versão mais recente.

Por vezes, mais de duas famílias ou versões podem ser suportado. As informações de suporte de SO convidado oficiais serão apresentados no [versões de SO de convidado do Azure e matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md).

## <a name="when-a-guest-os-family-or-version-is-retired"></a>Quando uma família de SO convidado ou a versão é retirado
Um SO convidado novo **família** é apresentado algum tempo após o lançamento de uma nova versão oficial do sistema operativo Windows Server. Sempre que uma nova família de SO convidado é introduzida, a Microsoft irá extinguir a família de SO convidado mais antiga.

Novo SO convidado **versões** são introduzidas sobre todos os meses para incorporar as atualizações mais recentes do MSRC. Devido às atualizações mensais regulares, uma versão de SO convidado normalmente está desativada cerca de 60 dias após a respetiva versão. Esta atividade mantém, pelo menos, duas versões de SO convidado para cada família disponível para utilização.

### <a name="process-during-a-guest-os-family-retirement"></a>Processo durante um extinção de família de SO convidado
Depois da extinção é anunciada, os clientes têm um período de transição"mês 12" antes da família de mais antiga oficialmente é removida do serviço. Desta vez transição pode ser expandida critério da Microsoft. Serão possível publicar atualizações no [versões de SO de convidado do Azure e matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md).

Um processo de extinção gradual irá começar meses seis (6) para o período de transição. Durante este período de tempo:

1. A Microsoft irá notificar os clientes da extinção.
2. A versão mais recente do Azure SDK não suporta a família de SO convidado extinto.
3. Novas implementações e redeployments dos serviços em nuvem não serão permitidas na família extinto

A Microsoft irá continuar a introduzir a nova versão de SO convidado incorporando as atualizações mais recentes do MSRC até ao último dia do período de transição, conhecido como "data de expiração". A data de expiração, irá não continuam a executar os serviços de nuvem suportados sob o SLA do Azure. A Microsoft tem o critério para forçar a atualização, elimine ou pare a esses serviços após essa data.

### <a name="process-during-a-guest-os-version-retirement"></a>Processo durante a extinção de uma versão de SO convidado
Se os clientes definido as respetivas SO convidado para atualizar automaticamente, nunca têm de preocupar com a lidar com versões de SO convidado. Podem sempre utilizar a versão mais recente do SO convidado.

Versões de SO convidado são lançadas todos os meses. Devido a taxa de versões regulares, cada versão tem um tempo de vida fixo.

Em 60 dias para o tempo de vida, é uma versão "*desativada*". "Desativado" significa que a versão é removida do portal. A versão já não pode ser definida no CSCFG ficheiro de configuração. As implementações existentes são deixadas em execução. Mas novas implementações e atualizações de código e a configuração para as implementações existentes não serão permitidas.

Algum tempo depois de se tornar "desativado", a versão de SO convidado "*expira*" e quaisquer instalações continuam a executar essa versão são force atualizado e definido para atualizar automaticamente o SO convidado. Expiração é feita em lotes para que o período de tempo de disablement expiração pode variar.

Estes pontos finais poderão ser estabelecidas mais critério da Microsoft para facilitar a transições de cliente. Quaisquer alterações serão comunicadas no [versões de SO de convidado do Azure e matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md).

### <a name="notifications-during-retirement"></a>Notificações durante a extinção
* **Família extinção** <br>Microsoft irá utilizar publicações no blogue e notificação do portal. Os clientes que ainda estão a utilizar uma família de SO convidado extinto serão notificados através de comunicação direta (correio eletrónico, mensagens de portais, chamada telefónica) aos administradores de serviço atribuído. Todas as alterações serão publicados para o [versões de SO de convidado do Azure e matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md).
* **Extinção de versão** <br>Todas as alterações e as datas ocorrem vai ser publicadas no [versões de SO de convidado do Azure e matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md), incluindo a versão, desativado e a expiração. Administradores de serviços irão receber e-mails, se tiverem implementações em execução uma versão de SO convidado desativado ou a família. A temporização dos mesmos pode variar. Geralmente, são pelo menos um mês anterior disablement, apesar deste temporização não é um SLA oficial.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
**Como pode a mitigar o impacto da migração?**

Recomendamos que utilize mais recente família de SO convidado para criar os seus serviços em nuvem.

1. Começar a planear a migração para uma família de mais recente antecipadamente.
2. Configure as implementações de teste temporário para testar o seu serviço de nuvem a executar a nova família.
3. Definir a sua versão de SO convidado **automática** (osVersion = * no [. cscfg](cloud-services-model-and-package.md#cscfg) ficheiro) para a migração para novas versões de SO convidado ocorre automaticamente.

**E se a minha aplicação web requer uma integração mais profunda com o SO?**

Se a arquitetura da aplicação web depende subjacentes funcionalidades do sistema operativo, utilize as capacidades de plataforma suportada, como [tarefas de arranque](cloud-services-startup-tasks.md) ou de outros mecanismos de extensibilidade. Em alternativa, pode também utilizar [Virtual Machines do Azure](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – infraestrutura como serviço), onde tem o responsável pela manutenção do sistema operativo subjacente.

## <a name="next-steps"></a>Passos seguintes
Reveja a versão mais recente [versões do SO convidado](cloud-services-guestos-update-matrix.md).

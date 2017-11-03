---
title: Como administrar a Cache de Redis do Azure | Microsoft Docs
description: "Saiba como efetuar tarefas de administração, tais como atualizações de reinício e a agenda para a Cache de Redis do Azure"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: 8c915ae6-5322-4046-9938-8f7832403000
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 07/05/2017
ms.author: sdanie
ms.openlocfilehash: 3352fec59d7dfbfab9b0416992a60f11d0ec2402
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-administer-azure-redis-cache"></a>Como administrar a Cache de Redis do Azure
Este tópico descreve como efetuar tarefas de administração como [reiniciando](#reboot) e [agendar atualizações](#schedule-updates) das suas instâncias de Cache de Redis do Azure.

## <a name="reboot"></a>Reiniciar
O **reiniciar** painel permite-lhe reiniciar um ou mais nós da cache. Esta capacidade de reinício permite-lhe testar a aplicação para resiliência se ocorrer uma falha de um nó de cache.

![Reiniciar](./media/cache-administration/redis-cache-administration-reboot.png)

Selecione os nós para reiniciar o computador e clique em **reiniciar**.

![Reiniciar](./media/cache-administration/redis-cache-reboot.png)

Se tiver uma cache premium com clustering ativado, pode selecionar quais shards da cache para reiniciar o computador.

![Reiniciar](./media/cache-administration/redis-cache-reboot-cluster.png)

Para reiniciar um ou mais nós da cache, selecione os nós pretendido e clique em **reiniciar**. Se tiver uma cache premium com clustering ativada, selecione os shards pretendidos para reiniciar o computador e, em seguida, clique em **reiniciar**. Após alguns minutos, o reinício de nós selecionados e são cópia online alguns minutos depois.

O impacto sobre as aplicações do cliente varia consoante quais os nós que reiniciar.

* **Mestre** - quando o nó mestre é falha de Cache de Redis do Azure, reinicializada para o nó de réplica e promove o mestre. Durante esta ativação pós-falha, poderá existir um curto intervalo em que as ligações podem falhar para a cache.
* **Multisservidor** - quando o nó subordinado é reiniciado, não existe, normalmente, não terá qualquer impacto para os clientes de cache.
* **Principal e subordinado** -quando são reiniciados, nós de cache, há perda de todos os dados na cache tanto as ligações para a cache falhar até que o nó principal ficar online novamente. Se tiver configurado [a persistência de dados](cache-how-to-premium-persistence.md), mais recente cópia de segurança é restaurada quando a cache de volta a ficar online, mas qualquer escreve de cache que ocorreram após a cópia de segurança mais recente são perdidas.
* **Nós de um premium da cache com clustering ativada** - quando reiniciar um ou mais nós de uma cache de premium com clustering ativada, o comportamento para os nós selecionados é o mesmo quando reiniciar o nó correspondente ou nós de uma cache agrupado.

> [!IMPORTANT]
> Reiniciar o computador está agora disponível para todos os escalões de preços.
> 
> 

## <a name="reboot-faq"></a>FAQ de reinício
* [O nó deve reiniciar para testar a minha aplicação?](#which-node-should-i-reboot-to-test-my-application)
* [Posso reiniciar a cache para limpar as ligações de cliente?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Irá perder dados da minha cache se posso fazer um reinício?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Posso reiniciar os meus cache com o PowerShell, CLI ou outras ferramentas de gestão?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
* [Os preços camadasm podem utilizar a funcionalidade de reinício?](#what-pricing-tiers-can-use-the-reboot-functionality)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>O nó deve reiniciar para testar a minha aplicação?
Para testar a resiliência da sua aplicação contra falhas do nó principal da cache, reinicie o **mestre** nós. Para testar a resiliência da sua aplicação contra falhas de nó secundário, reinicie o **multisservidor** nós. Para testar a resiliência da sua aplicação contra falha total da cache, reiniciar **ambos** nós.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Posso reiniciar a cache para limpar as ligações de cliente?
Sim, se o reinício da cache todas as ligações de cliente são eliminadas. Pode ser útil nos casos em que todas as ligações de cliente são utilizadas cópias de segurança devido a um erro de lógica ou de erros da aplicação de cliente a ser reiniciado. Cada escalão de preço tem diferentes [limites de ligação de cliente](cache-configure.md#default-redis-server-configuration) para os vários tamanhos e, uma vez estes limites são atingidos, não existem mais ligações de cliente são aceites. Reiniciar a cache de uma forma para limpar todas as ligações de cliente.

> [!IMPORTANT]
> Se reiniciar o computador de cache para limpar as ligações de cliente, stackexchange. redis automaticamente volta a ligar depois do nó de Redis está novamente online. Se o problema subjacente não for resolvido, as ligações de cliente podem continuar a ser utilizada a cópia de segurança.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Irá perder dados da minha cache se posso fazer um reinício?
Se reiniciar tanto o **mestre** e **multisservidor** nós, todos os dados na cache (ou em que partições horizontais se estiver a utilizar uma cache premium com clustering ativado) é perdido. Se tiver configurado [a persistência de dados](cache-how-to-premium-persistence.md), mais recente cópia de segurança será restaurada quando a cache de volta a ficar online, mas qualquer gravações de cache que ocorreram após foi efetuada a cópia de segurança são perdidas.

Se reiniciar apenas um de nós, dados não são normalmente perdidos, mas ainda pode ser. Para o exemplo, se o nó mestre é reiniciado e uma operação de escrita de cache está em curso, os dados da operação de escrita de cache é perdido. Outro cenário para a perda de dados seria se reiniciar o computador um nó e o outro nó acontece Ir para baixo devido a uma falha ao mesmo tempo. Para mais informações sobre as causas possíveis para a perda de dados, consulte [o que aconteceu Redis os meus dados?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Posso reiniciar os meus cache com o PowerShell, CLI ou outras ferramentas de gestão?
Sim, para o PowerShell instruções consulte [reiniciar uma cache de Redis](cache-howto-manage-redis-cache-powershell.md#to-reboot-a-redis-cache).

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>Os preços camadasm podem utilizar a funcionalidade de reinício?
Reiniciar o computador está disponível para todos os escalões de preços.

## <a name="schedule-updates"></a>Atualizações agendadas
O **agendar atualizações** painel permite-lhe designar uma janela de manutenção para a sua cache do escalão Premium. Quando a janela de manutenção é especificada, quaisquer atualizações do servidor de Redis são efetuadas durante este período. 

> [!NOTE] 
> A janela de manutenção só se aplica a Redis atualizações do servidor e não para qualquer Azure atualizações ou atualizações do sistema operativo das VMs que a cache do anfitrião.
> 
> 

![Atualizações agendadas](./media/cache-administration/redis-schedule-updates.png)

Para especificar uma janela de manutenção, verifique os dias pretendidos e especifique a hora de início da janela de manutenção para cada dia e clique em **OK**. Tenha em atenção que o tempo de janela de manutenção está em UTC. 

> [!NOTE]
> A janela de manutenção predefinido para atualizações é de cinco horas. Este valor não é configurável do portal do Azure, mas pode configurá-lo no PowerShell utilizando o `MaintenanceWindow` parâmetro do [New-AzureRmRedisCacheScheduleEntry](/powershell/module/azurerm.rediscache/new-azurermrediscachescheduleentry) cmdlet. Para obter mais informações, consulte [posso gerir com o PowerShell, CLI ou outras ferramentas de gestão de atualizações agendadas?](#can-i-manage-scheduled-updates-using-powershell-cli-or-other-management-tools)
> 
> 

## <a name="schedule-updates-faq"></a>Agendar atualizações FAQ
* [Ao atualizações ocorrer se não utilizar a funcionalidade de atualizações de agenda?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Tipo de atualizações que são efetuadas durante a janela de manutenção agendada?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Pode gerir com o PowerShell, CLI ou outras ferramentas de gestão de atualizações agendadas?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
* [Os preços camadasm podem utilizar a funcionalidade de atualizações de agenda?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Ao atualizações ocorrer se não utilizar a funcionalidade de atualizações de agenda?
Se não especificar uma janela de manutenção, as atualizações podem ser efetuadas em qualquer altura.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Tipo de atualizações que são efetuadas durante a janela de manutenção agendada?
Apenas Redis servidor efectuadas durante a janela de manutenção agendada. Não é aplicável a janela de manutenção para atualizações do Azure ou atualizações para o sistema operativo VM.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Posso atualizações agendadas geridas com o PowerShell, CLI ou outras ferramentas de gestão?
Sim, pode gerir as atualizações agendadas utilizando os seguintes cmdlets do PowerShell:

* [Get-AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/get-azurermrediscachepatchschedule)
* [Novo AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/new-azurermrediscachepatchschedule)
* [Novo AzureRmRedisCacheScheduleEntry](/powershell/module/azurerm.rediscache/new-azurermrediscachescheduleentry)
* [Remover AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/remove-azurermrediscachepatchschedule)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality"></a>Os preços camadasm podem utilizar a funcionalidade de atualizações de agenda?
O **agendar atualizações** funcionalidade só está disponível no escalão de preços do premium.

## <a name="next-steps"></a>Passos seguintes
* Explorar mais [escalão premium de Cache de Redis do Azure](cache-premium-tier-intro.md) funcionalidades.


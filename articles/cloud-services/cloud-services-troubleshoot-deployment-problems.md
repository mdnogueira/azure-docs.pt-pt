---
title: "Resolver problemas de implementação do serviço de nuvem | Microsoft Docs"
description: "Existem alguns problemas comuns que poderá ter quando implementar um serviço em nuvem do Azure. Este artigo fornece soluções para algumas delas."
services: cloud-services
documentationcenter: 
author: simonxjx
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: a18ae415-0d1c-4bc4-ab6c-c1ddea02c870
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 7/26/2017
ms.author: v-six
ms.openlocfilehash: 29529978a2f6a1454c7a97b5f5baac214a90d63b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>Resolver problemas de implementação do serviço de nuvem
Quando implementar um pacote de aplicação de serviço em nuvem do Azure, pode obter informações sobre a implementação do **propriedades** painel no portal do Azure. Pode utilizar os detalhes neste painel para o ajudar a resolver problemas relacionados com o serviço em nuvem, e pode fornecer estas informações para suportar o Azure ao abrir um novo pedido de suporte.

Pode encontrar o **propriedades** painel da seguinte forma:

* No portal do Azure, clique a implementação do seu serviço em nuvem, clique em **todas as definições**e, em seguida, clique em **propriedades**.
* No portal clássico do Azure, clique a implementação do seu serviço em nuvem, clique em **DASHBOARD**, localizada no canto inferior direito da página (em **leitura rápida**). Lembre-se de que não há qualquer etiqueta "Propriedades" neste painel.

> [!NOTE]
> Pode copiar o conteúdo a **propriedades** painel para a área de transferência clicando no ícone no canto superior direito do painel.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problema: não é possível aceder a minha Web site, mas os meus implementação ser iniciada e todas as instâncias de função, estará pronto
A ligação do URL de Web site apresentada no portal não inclui a porta. A porta predefinida para Web sites é 80. Se a aplicação está configurada para executar uma porta diferente, tem de adicionar o número de porta correta para o URL ao aceder ao Web site.

1. No portal do Azure, clique a implementação do seu serviço em nuvem.
2. No **propriedades** painel do portal do Azure, verifique as portas para as instâncias de função (em **pontos finais de entrada**).
3. Se a porta não for 80, adicione o valor de porta correta para o URL ao aceder à aplicação. Para especificar uma porta não predefinida, escreva o URL, seguido de dois pontos (:), seguido do número de porta, sem espaços.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problema: Meu instâncias de função recicladas sem enviar-me fazer nada
Serviço autorrecuperação ocorre automaticamente quando o Azure Deteta nós de problema e, por conseguinte, move as instâncias de função para novos nós. Se isto ocorre, poderá ver as instâncias da função Reciclagem automaticamente. Para saber se autorrecuperação do serviço ocorreu:

1. No portal do Azure, clique a implementação do seu serviço em nuvem.
2. No **propriedades** painel do portal do Azure, reveja as informações e determinar se autorrecuperação do serviço ocorreu durante o período de tempo observado as funções de reciclagem.

Funções também reciclará aproximadamente uma vez por mês durante SO do anfitrião e atualizações de SO convidado.  
Para obter mais informações, consulte a mensagem de blogue [função instância reinicia devido a atualizações do SO](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problema: Posso não é possível fazer uma alternância de VIP e receber um erro
Uma alternância de VIP não é permitida se uma atualização da implementação estiver em curso. Atualizações de implementação podem ocorrer automaticamente quando:

* Um novo sistema de operativo convidado está disponível e estão configurados para atualizações automáticas.
* Ocorre a autorrecuperação do serviço.

Para saber se uma atualização automática está a impedir a efetuar uma alternância de VIP:

1. No portal do Azure, clique a implementação do seu serviço em nuvem.
2. No **propriedades** painel do portal do Azure, observe o valor de **estado**. Se for **pronto**, em seguida, verifique **última operação** ver se um recentemente aconteceu que poderá impedir a alternância de VIP.
3. Repita os passos 1 e 2 para a implementação de produção.
4. Se uma atualização automática está em curso, aguarde que termine antes de tentar efetuar a alternância de VIP.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problema: Uma instância de função é ciclo entre iniciado, inicialização, ocupado e parado
Esta condição poderá indicar um problema com o código, o pacote ou o ficheiro de configuração da aplicação. Nesse caso, deverá conseguir ver o estado de alteração em alguns minutos e o portal do Azure poderá ser algo semelhante ao seguinte **Reciclagem**, **ocupado**, ou **Initializing**. Isto indica que existe algo errado com a aplicação que consiste em manter a instância de função a execução.

Para obter mais informações sobre como resolver problemas para este problema, consulte a mensagem de blogue [dados de diagnóstico de computação do Azure PaaS](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) e [problemas comuns que causam a Reciclagem de funções](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Problema: A minha aplicação deixou de funcionar
1. No portal do Azure, clique a instância de função.
2. No **propriedades** painel do portal do Azure, considere as seguintes condições para resolver o problema:
   * Se a instância de função recentemente parou (pode verificar o valor de **abortar contagem**), foi possível atualizar a implementação. Espere para ver se a instância de função retoma a funcionar no seu próprio.
   * Se a instância de função é **ocupado**, verifique o código da aplicação para ver se o [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck) evento é processado. Poderá ter de adicionar ou corrigir alguns códigos que processa este evento.
   * Percorrer os dados de diagnóstico e resolução de problemas de cenários do blogue [dados de diagnóstico de computação do Azure PaaS](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

> [!WARNING]
> Se reciclar o seu serviço em nuvem, repor as propriedades para a implementação, apagar eficazmente as informações para o problema original.
>
>

## <a name="next-steps"></a>Passos seguintes
Ver mais [artigos de resolução de problemas](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-allocation-failures) para serviços em nuvem.

Para saber como resolver problemas de função Serviço de nuvem, utilizando os dados de diagnóstico do computador PaaS do Azure, consulte [série de blogues de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

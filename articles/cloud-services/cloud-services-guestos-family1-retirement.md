---
title: "Família de SO convidado 1 extinção Repare | Microsoft Docs"
description: "Fornece informações sobre quando ocorreu a extinção de 1 de família de SO de convidado do Azure e como determinar se são afetados"
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: 
ms.assetid: 37b422e9-0713-4a81-a942-f553ef478064
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: 3178a09dab1cb972a3460d54dc9908fb95cce68b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="guest-os-family-1-retirement-notice"></a>Aviso de extinção de 1 de família de SO convidado
A extinção de 1 de família do SO foi anunciada pela primeira vez no dia 1 de Junho de 2013.

**2 de Setembro de 2014** o Azure sistema operativo (SO convidado) família 1. x, o que se baseia no sistema operativo Windows Server 2008, oficialmente foi extinguido. Todas as tentativas para implementar novos serviços ou atualizar os serviços existentes com família 1 irão falhar com uma mensagem de erro informando-o de que a família de SO convidado 1 foi extinguido.

**3 de Novembro de 2014** suporte expandido para 1 de família de SO convidado terminou e é totalmente retirado. Todos os serviços ainda na família 1 serão afetados. Pode parar os serviços em qualquer altura. Não há nenhuma garantia que seus serviços serão continua a ser executado, a menos que tem de atualizar manualmente-los por si.

Se tiver dúvidas adicionais, visite o [fóruns de serviços em nuvem](http://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) ou [contacte o suporte do Azure](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>São afetadas?
Os serviços de nuvem são afetados se qualquer um dos seguintes se aplicar:

1. Ter um valor de "osFamily ="1"ou não explicitamente especificado no ficheiro serviceconfiguration. Cscfg do serviço em nuvem.
2. Não dispõe de um valor para osFamily ou não explicitamente especificado no ficheiro serviceconfiguration. Cscfg do serviço em nuvem. Atualmente, o sistema utiliza neste caso, o valor predefinido de "1".
3. O portal do Azure apresenta o valor de família do sistema operativo convidado, como "Windows Server 2008".

Para determinar quais dos seus serviços em nuvem estão em execução que família de SO, pode executar o seguinte script no Azure PowerShell, embora tem [configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) primeiro. Para obter mais informações sobre o script, consulte [Azure convidado SO família 1 final de vida: Junho de 2014](http://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Os serviços de nuvem serão afetados pela extinção de 1 de família de SO se a coluna de osFamily na saída do script está vazia ou contém um "1".

## <a name="recommendations-if-you-are-affected"></a>Recomendações se for afectado
Recomendamos que migre as funções do serviço em nuvem a uma das famílias de SO de convidado suportados:

**SO convidado 4. x família** -Windows Server 2012 R2 *(recomendado)*

1. Certifique-se de que a aplicação está a utilizar o SDK 2.1 ou posterior com o .NET framework 4.0, 4.5 ou 4.5.1.
2. Definir o atributo osFamily para "4" no ficheiro serviceconfiguration. Cscfg e volte a implementar o seu serviço em nuvem.

**SO convidado família 3** -Windows Server 2012

1. Certifique-se de que a aplicação está a utilizar o SDK 1.8 ou posterior com o .NET framework 4.0 ou 4.5.
2. Definir o atributo osFamily "3" no ficheiro serviceconfiguration. Cscfg e volte a implementar o seu serviço em nuvem.

**SO convidado 2 família** -Windows Server 2008 R2

1. Certifique-se de que a aplicação está a utilizar SDK 1.3 e acima com o .NET framework 3.5 ou 4.0.
2. Definir o atributo osFamily "2" no ficheiro serviceconfiguration. Cscfg e volte a implementar o seu serviço em nuvem.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Suporte alargado para 1 de família de SO convidado terminou 3 de Novembro de 2014
Serviços cloud na família de SO convidado 1 já não são suportados. Migração desativada família 1 logo que possível, para evitar a interrupção do serviço.  

## <a name="next-steps"></a>Passos seguintes
Reveja a versão mais recente [versões do SO convidado](cloud-services-guestos-update-matrix.md).

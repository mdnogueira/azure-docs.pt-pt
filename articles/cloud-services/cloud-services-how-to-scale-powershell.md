---
title: "Dimensionar um serviço em nuvem do Azure no Windows PowerShell | Microsoft Docs"
description: "(clássica) Saiba como utilizar o PowerShell para dimensionar a uma função da web ou a função de trabalho entrada ou saída no Azure."
services: cloud-services
documentationcenter: 
author: mmccrory
manager: timlt
editor: 
ms.assetid: ee37dd8c-6714-4c61-adb8-03d6bbf76c9a
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: mmccrory
ms.openlocfilehash: a7ae8ff202d403dff19b8c9a6a09492235db27ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Como dimensionar um serviço em nuvem no PowerShell

Pode utilizar o Windows PowerShell para dimensionar uma função da web ou função de trabalho ou reduzir adicionando ou removendo instâncias.  

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Antes de poder executar quaisquer operações na sua subscrição através do PowerShell, tem de iniciar sessão:

```powershell
Add-AzureAccount
```

Se tiver várias subscrições associadas à sua conta, poderá ter de alterar a subscrição atual, dependendo de onde reside o serviço em nuvem. Para verificar a subscrição atual, execute:

```powershell
Get-AzureSubscription -Current
```

Se precisar de alterar a subscrição atual, execute:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>A contagem atual de instâncias para a função de verificação

Para verificar o estado atual da sua função, execute:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Deve regressar à informações sobre a função, incluindo o respetivo número de versão e instância SO atual. Neste caso, a função tem uma única instância.

![Informações sobre a função](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Ampliar a função ao adicionar mais instâncias

Para ampliar a função, passar o número pretendido de instâncias como o **contagem** parâmetro para o **conjunto AzureRole** cmdlet:

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

Os blocos de cmdlet momentaneamente ao novas instâncias são aprovisionados e iniciados. Durante este período, se abrir uma nova janela do PowerShell e chamada **Get-AzureRole** conforme mostrado anteriormente, verá a nova contagem de instâncias de destino. E se inspecionar o estado de função no portal, deverá ver a nova instância iniciar cópia de segurança:

![Instância VM a partir do portal](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Depois das novas instâncias tem sido iniciado, o cmdlet irá devolver com êxito:

![Sucesso de aumento de instância de função](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Dimensionar na função removendo instâncias

Pode dimensionar numa função removendo as instâncias da mesma forma. Definir o **contagem** parâmetro no **conjunto AzureRole** para o número de instâncias que pretende ter depois de concluída a escala na operação.

## <a name="next-steps"></a>Passos seguintes

Não é possível configurar o dimensionamento automático para serviços em nuvem a partir do PowerShell. Para tal, consulte [como auto escala um serviço em nuvem](cloud-services-how-to-scale-portal.md).

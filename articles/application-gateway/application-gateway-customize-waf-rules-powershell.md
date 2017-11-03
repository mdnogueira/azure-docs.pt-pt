---
title: "Personalizar regras de firewall de aplicação web no Gateway de aplicação do Azure - PowerShell | Microsoft Docs"
description: "Este artigo fornece informações sobre como personalizar regras de firewall de aplicação web no Gateway de aplicação com o PowerShell."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: 97c3fe6f0b7a4d9b967b44bf819a6f25598febc9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="customize-web-application-firewall-rules-through-powershell"></a>Personalizar regras de firewall de aplicação web através do PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [CLI 2.0 do Azure](application-gateway-customize-waf-rules-cli.md)

A firewall de aplicações do Gateway de aplicação do Azure web (WAF) fornece proteção para as aplicações web. Estas proteções são fornecidas pela aplicação do abra Web projeto segurança (OWASP) Core regra definido (CR). Algumas regras podem causar falsos positivos e bloquear o tráfego real. Por este motivo, o Gateway de aplicação fornece a capacidade para personalizar grupos de regras e as regras. Para obter mais informações sobre os grupos de regra específica e regras, consulte [lista de grupos de CR regra de firewall de aplicações web e regras](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Grupos de regras de vista e regras

Os exemplos de código seguintes mostram como ver as regras e grupos de regras que são configuráveis num gateway de aplicação com capacidade WAF.

### <a name="view-rule-groups"></a>Grupos de regras de vista

O exemplo seguinte mostra como ver grupos de regra:

```powershell
Get-AzureRmApplicationGatewayAvailableWafRuleSets
```

O resultado seguinte é uma resposta truncada do exemplo anterior:

```
OWASP (Ver. 3.0):

    REQUEST-910-IP-REPUTATION:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            910011     Rule 910011
            910012     Rule 910012
            ...        ...

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            ...        ...

OWASP (Ver. 2.2.9):

    crs_20_protocol_violations:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            960911     Invalid HTTP Request Line
            981227     Apache Error: Invalid URI in Request.
            960000     Attempted multipart/form-data bypass
            ...        ...
```

## <a name="disable-rules"></a>Desativar regras

O exemplo a seguir desativa regras `910018` e `910017` num gateway de aplicação:

```azurecli
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="next-steps"></a>Passos seguintes

Depois de configurar as regras desativadas, pode saber como ver os registos de WAF. Para obter mais informações, consulte [diagnóstico do Gateway de aplicação](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png

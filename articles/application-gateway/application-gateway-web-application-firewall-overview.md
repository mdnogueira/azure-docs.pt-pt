---
title: "Introdução à firewall de aplicações Web (WAF) para o Gateway de Aplicação do Azure| Microsoft Docs"
description: "Esta página proporciona uma descrição geral da firewall de aplicações Web (WAF) para o Gateway de Aplicação"
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 04b362bc-6653-4765-86f6-55ee8ec2a0ff
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: amsriva
ms.openlocfilehash: 50863b60f2843f033bdb07f4564d937cd6f0b7be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="web-application-firewall-waf"></a>Firewall de aplicações Web (WAF)

Firewall de aplicações Web (WAF) é uma funcionalidade de Gateway de Aplicação que fornece proteção centralizada das suas aplicações Web a partir de exploits e vulnerabilidades comuns. 

A firewall de aplicações Web baseia-se nas regras dos [conjuntos de regras de núcleo OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9. Cada vez mais, as aplicações Web são alvo de ataques maliciosos que exploram vulnerabilidades conhecidas comuns. Destas vulnerabilidades, são frequentes os ataques de injeção de SQL, scripting entre sites, entre muitas outras. Impedir este tipo de ataques ao código das aplicações constitui um desafio e exige uma manutenção, correção e monitorização rigorosas em várias camadas da topologia da aplicação. Uma firewall de aplicações Web centralizada ajuda a simplificar em muito a gestão da segurança e confere aos administradores de aplicações uma maior garantia de proteção contra as ameaças ou intrusões. Uma solução WAF também pode reagir mais rapidamente a uma ameaça de segurança ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações Web individualmente. Os gateways de aplicações existentes podem ser facilmente convertidos num gateway de aplicação com firewall de aplicações Web ativada.

![imageURLroute](./media/application-gateway-web-application-firewall-overview/WAF1.png)

O Gateway de Aplicação atua como um controlador de entrega de aplicações e oferece terminação de SSL, afinidade de sessão baseada em cookies, distribuição de carga round robin, encaminhamento baseado em conteúdo, a capacidade de alojar vários sites e melhoramentos de segurança. Os melhoramentos de segurança que o Gateway de Aplicação oferece incluem gestão de política SSL e suporte SSL ponto a ponto. Agora, a segurança das aplicações está mais fortalecida, com a WAF (firewall de aplicações Web) a ser integrada diretamente na oferta do ADC. Desta forma, conseguimos fornecer uma localização central fácil de configurar que permite gerir e proteger as suas aplicações Web contra vulnerabilidades Web conhecidas.

## <a name="benefits"></a>Benefícios

Seguem-se as principais vantagens proporcionadas pelo Gateway de Aplicação e pela firewall de aplicações Web:

### <a name="protection"></a>Proteção

* Protege a sua aplicação Web de vulnerabilidades e ataques Web sem modificar o código de back-end.

* Protege várias aplicações Web em simultâneo por trás de um gateway de aplicação. O gateway de aplicação suporta o alojamento de até 20 Web sites por trás de um único gateway, que podem ser protegidos contra ataques Web com a WAF.

### <a name="monitoring"></a>Monitorização

* Utilize um registo da WAF em tempo real para monitorizar a sua aplicação Web contra ataques. Este registo está integrado no [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) para registar alertas e registos da WAF e monitorizar tendências facilmente.

* A WAF vai ser integrada no Centro de Segurança do Azure brevemente. O Centro de Segurança do Azure permite obter uma visão central do estado de segurança de todos os seus recursos do Azure.

### <a name="customization"></a>Personalização

* A capacidade para personalizar regras e grupos de regras WAF para se adequarem às necessidades da sua aplicação e eliminar falsos positivos.

## <a name="features"></a>Funcionalidades

A firewall de aplicação Web está pré-configurada com CRS 3.0, por predefinição, ou pode optar por utilizar a 2.2.9. A CRS 3.0 oferece menos falsos positivos do que a 2.2.9. É disponibilizada a capacidade de [personalizar a regras para se adequarem às suas necessidades](application-gateway-customize-waf-rules-portal.md). Algumas das vulnerabilidades Web comuns contra as quais a firewall de aplicações Web protege incluem:

* Proteção contra injeção de SQL
* Proteção contra scripting entre sites
* Proteção contra Ataques Web comuns, como, por exemplo, injeção de comandos, contrabando de pedidos HTTP, divisão de respostas HTTP e ataques remotos de inclusão de ficheiros
* Proteção contra violações de protocolo HTTP
* Proteção contra anomalias de protocolo HTTP, como agente de utilizador de anfitrião e cabeçalhos de aceitação em falta
* Prevenção de contra bots, crawlers e scanners
* Deteção de configurações de aplicação incorretas comuns (ou seja, Apache, IIS, etc.)

Para obter uma lista mais detalhada das regras e das respetivas proteções, veja [Core rule sets](#core-rule-sets) (Conjuntos de regras principais).

### <a name="core-rule-sets"></a>Conjuntos de regras principais

O Gateway de Aplicação suporta dois conjuntos de regras: CRS 3.0 e CRS 2.2.9. Estes conjuntos de regras principais são coleções de regras que protegem as suas aplicações Web contra atividades maliciosas.

#### <a name="owasp30"></a>OWASP_3.0

O conjunto de regras principais 3.0 fornecido tem 13 grupos de regras, conforme mostrado na tabela seguinte. Cada um destes grupos de regras contém várias regras, que podem ser desativadas.

|RuleGroup|Descrição|
|---|---|
|**[REQUEST-910-IP-REPUTATION](application-gateway-crs-rulegroups-rules.md#crs910)**|Contém regras para proteger contra remetentes de spam ou atividades maliciosas conhecidas.|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|Contém regras para bloquear métodos (PUT, PATCH< ..)|
|**[REQUEST-912-DOS-PROTECTION](application-gateway-crs-rulegroups-rules.md#crs912)**| Contém regras para proteger contra ataques Denial of Service (DoS).|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**| Contém regras para proteger contra analisadores de portas e de ambientes.|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|Contém regras para proteger contra problemas de protocolos e codificação.|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|Contém regras para proteger contra injeção de cabeçalho, contrabando de pedidos e divisão de respostas.|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|Contém regras para proteger contra ataques de ficheiros e caminhos.|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|Contém regras para proteger contra Inclusão de Ficheiros Remota (RFI).|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|Contém regras para proteger contra Execução de Código Remota.|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|Contém regras para proteger contra ataques de injeção PHP.|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|Contém regras para proteger contra scripts entre sites.|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|Contém regras para proteger contra ataques de injeção de SQL Server.|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|Contém regras para proteger contra Ataques de Fixação de Sessão.|

#### <a name="owasp229"></a>OWASP_2.2.9

O conjunto de regras principais 2.2.9 fornecido tem dez grupos de regras, conforme mostrado na tabela seguinte. Cada um destes grupos de regras contém várias regras, que podem ser desativadas.

|RuleGroup|Descrição|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|Contém regras para proteger contra violações de protocolo (carateres inválidos, GET com um corpo de pedido, etc.)|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|Contém regras para proteger contra informações de cabeçalho incorretas.|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|Contém regras para proteger contra argumentos ou ficheiros que excedem limitações.|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|Contém regras para proteger contra métodos, cabeçalhos e tipos de ficheiros restringidos. |
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|Contém regras para proteger contra crawlers e scanners da Web.|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|Contém regras para proteger contra ataques genéricos (fixação de sessão, inclusão de ficheiros remota, injeção PHP, etc.)|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|Contém regras para proteger contra ataques de injeção de SQL.|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|Contém regras para proteger contra scripts entre sites.|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|Contém uma regra para proteger contra ataques de caminho tranversais.|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|Contém regras para proteger contra trojans de backdoor.|

### <a name="waf-modes"></a>Modos de WAF

A WAF do Gateway de Aplicação pode ser configurada para ser executada nos dois modos seguintes:

* **Modo de deteção** – Se configurar a WAF do Gateway de Aplicação para ser executada no modo de deteção, esta monitoriza e regista todos os alertas de ameaça num ficheiro de registo. Os Diagnósticos de registo do Gateway de Aplicação devem ser ativados através da secção **Diagnósticos**. Também deverá certificar de que o registo WAF está selecionado e ativado. Quando é executada no modo de deteção, a firewall de aplicações Web não bloqueia pedidos de entrada.
* **Modo de prevenção** – Se configurar o Gateway de Aplicação para ser executado no modo de prevenção, este bloqueia ativamente intrusões e ataques detetados pelas respetivas regras. O atacante recebe uma exceção de acesso não autorizado 403 e a ligação é terminada. O modo de prevenção continua a registar os ataques nos registos WAF.

### <a name="application-gateway-waf-reports"></a>Monitorização da WAF

É importante monitorizar o estado de funcionamento do gateway de aplicação. A monitorização do estado de funcionamento da sua firewall de aplicações Web e das aplicações que esta protege é proporcionada através do registo e da integração no Azure Monitor, no Centro de Segurança do Azure (brevemente) e no Log Analytics.

![diagnósticos](./media/application-gateway-web-application-firewall-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Cada registo do gateway de aplicação é integrado no [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).  Desta forma, pode acompanhar as informações de diagnóstico, incluindo alertas e registos da WAF.  Esta capacidade é disponibilizada no recurso do Gateway da Aplicação no portal, no separador **Diagnósticos** ou diretamente através do serviço Azure Monitor. Para saber mais sobre como permitir os registos de diagnósticos no Gateway de Aplicação, veja [Application Gateway diagnostics](application-gateway-diagnostics.md) (Diagnóstico do Gateway de Aplicação)

#### <a name="azure-security-center"></a>Centro de Segurança do Azure

O [Centro de Segurança do Azure](../security-center/security-center-intro.md) ajuda-o a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. O gateway de aplicação [está agora integrado no Centro de Segurança do Azure](application-gateway-integration-security-center.md). O Centro de Segurança do Azure analisa o seu ambiente para detetar aplicações Web desprotegidas. Já pode recomendar o WAF do gateway de aplicação para proteger estes recursos vulneráveis. Pode criar diretamente o WAF do gateway de aplicação a partir do Centro de Segurança do Azure.  Estas instâncias do WAF são integradas no Centro de Segurança do Azure e irão enviar alertas e informações do estado de funcionamento para o Centro de Segurança do Azure para os relatórios.

![figura 1](./media/application-gateway-web-application-firewall-overview/figure1.png)

#### <a name="logging"></a>Registo

A WAF do Gateway de Aplicação fornece relatórios detalhados sobre cada ameaça detetada. Os registos estão integrados no Diagnóstico do Azure e os registos e alertas são registados no formato json. Estes registos podem ser integrados no [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md).

![imageURLroute](./media/application-gateway-web-application-firewall-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>Preços da SKU da WAF do Gateway de Aplicação

A firewall de aplicações Web está disponível num novo WAF SKU. Este SKU só está disponível no modelo de aprovisionamento do Azure Resource Manager e não no modelo de implementação clássica. Além disso, o WAF SKU vem apenas em tamanhos de instâncias do gateway de aplicação médios e grandes. Todos os limites para o gateway de aplicação também se aplicam ao WAF SKU. O preço baseia-se no custo de instância de gateway por hora e no custo do processamento de dados. O preço de gateway por hora do WAF SKU é diferente dos custos do Standard SKU e está disponível em [Detalhes dos preços do Gateway da Aplicação](https://azure.microsoft.com/pricing/details/application-gateway/). Os custos de processamento permanecem iguais. Não existem custos relacionados com regras ou grupos de regras. Pode proteger várias aplicações Web atrás da mesma firewall de aplicações Web e não há custos adicionais para o suporte de várias aplicações. 

A faturação da WAF entra em vigor a 05/05/2017; até lá, os gateways do WAF SKU continuam a ser cobrados às taxas standard.

## <a name="next-steps"></a>Passos seguintes

Após saber mais sobre as capacidades da WAF, visite [How to configure web application firewall on Application Gateway](application-gateway-web-application-firewall-portal.md) (Como configurar a firewall de aplicações Web no Gateway da Aplicação).


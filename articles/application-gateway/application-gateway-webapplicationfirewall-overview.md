---
title: "Introdução à Firewall de Aplicações Web (WAF) para o Gateway de Aplicação | Microsoft Docs"
description: "Esta página proporciona uma descrição geral da Firewall de Aplicações Web (WAF) para o Gateway de Aplicação "
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
ms.date: 12/16/2016
ms.author: amsriva
translationtype: Human Translation
ms.sourcegitcommit: ce40a93372205a4b7c6b0c753ebf30c2b3d51d7a
ms.openlocfilehash: 2d8e851810b4ec5b7ae3f3b17c4aee11d7e51de4


---

# <a name="application-gateway-web-application-firewall-preview"></a>Firewall de Aplicações Web do Gateway de Aplicação (pré-visualização)

A Firewall de aplicações Web (WAF) é uma funcionalidade do gateway de aplicação do Azure que protege as aplicações Web que tiram partido do gateway de aplicação para as funções de Controlo de Entrega de Aplicação (ADC) padrão. A Firewall de aplicações Web fá-lo ao protegê-las contra a maioria das 10 principais vulnerabilidades Web da OWASP. Cada vez mais, as aplicações Web são alvo de ataques maliciosos que exploram vulnerabilidades conhecidas comuns. Destas vulnerabilidades, são frequentes os ataques de injeção de SQL, scripting entre sites, entre muitas outras. Impedir este tipo de ataques ao código das aplicações constitui um desafio e exige uma manutenção, correção e monitorização rigorosas em várias camadas da topologia da aplicação. Uma firewall de aplicações Web centralizada contra ataques Web simplifica em muito a gestão da segurança e confere à aplicação uma maior garantia de proteção contra as ameaças de intrusão. Uma solução WAF também pode reagir mais rapidamente a uma ameaça de segurança ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações Web individualmente. Os gateways de aplicação existentes podem ser convertidos num gateway de aplicação com a firewall de aplicações Web facilmente.

![imageURLroute](./media/application-gateway-webapplicationfirewall-overview/WAF1.png)

O Gateway de Aplicação atua como um controlador de entrega de aplicações e oferece terminação de SSL, afinidade de sessão baseada em cookies, distribuição de carga round robin, encaminhamento baseado em conteúdo, a capacidade de alojar vários sites e melhoramentos de segurança. Os melhoramentos de segurança que o Gateway de Aplicação oferece incluem gestão de política SSL e suporte SSL ponto a ponto. Estamos a reforçar as capacidades de segurança das aplicações no nosso serviço através da integração direta da WAF (firewall de aplicações Web) na oferta ADC. Desta forma, conseguimos fornecer uma localização central fácil de configurar que permite gerir e proteger as suas aplicações Web contra vulnerabilidades Web conhecidas.

Configurar a WAF no gateway de aplicação garante-lhe os seguintes benefícios:

* Protege a sua aplicação Web de vulnerabilidades e ataques Web sem modificar o código de back-end.
* Protege várias aplicações Web em simultâneo por trás de um gateway de aplicação. O gateway de aplicação suporta o alojamento de até 20 sites por trás de um único gateway, o que permite protegê-los a todos contra ataques Web.
* Monitoriza a sua aplicação Web contra ataques através de relatórios em tempo real gerados pelos registos WAF do gateway de aplicação.
* Determinados controlos de conformidade requerem que todos os pontos finais com acesso à Internet sejam protegidos por uma solução WAF. Ao utilizar o gateway de aplicação com a WAF ativada, pode satisfazer estes requisitos de conformidade.

## <a name="overview"></a>Descrição geral

A WAF do Gateway de Aplicação é oferecido num novo SKU (SKU da WAF) e vem pré-configurado com ModSecurity e o Conjunto de Regras Centrais da OWASP para garantir proteção de linha de base contra a maioria das 10 principais vulnerabilidades Web da OWASP.

* Proteção contra injeção de SQL
* Proteção contra scripting entre sites
* Proteção contra Ataques Web comuns, como, por exemplo, injeção de comandos, contrabando de pedidos HTTP, divisão de respostas HTTP e ataques remotos de inclusão de ficheiros
* Proteção contra violações de protocolo HTTP
* Proteção contra anomalias de protocolo HTTP, como agente de utilizador de anfitrião e cabeçalhos de aceitação em falta
* Prevenção de contra bots, crawlers e scanners
* Deteção de configurações de aplicação incorretas comuns (ou seja, Apache, IIS, etc.)

## <a name="waf-modes"></a>Modos de WAF

A WAF do Gateway de Aplicação pode ser configurada para ser executada nos dois modos seguintes:

* **Modo de deteção** – Se configurar a WAF do Gateway de Aplicação para ser executada no modo de deteção, esta monitoriza e regista todos os alertas de ameaça num ficheiro de registo. Tem de aceder à secção Diagnósticos para se certificar de que o registo de diagnósticos do Gateway de Aplicação está ativado. Também deverá certificar de que o registo WAF está selecionado e ativado.
* **Modo de prevenção** – Se configurar o Gateway de Aplicação para ser executado no modo de prevenção, este bloqueia ativamente intrusões e ataques detetados pelas respetivas regras. O atacante recebe uma exceção de acesso não autorizado 403 e a ligação é terminada. O modo de prevenção continua a registar os ataques nos registos WAF.

## <a name="application-gateway-waf-reports"></a>Relatórios da WAF do Gateway de Aplicação

A WAF do Gateway de Aplicação fornece relatórios detalhados sobre cada ameaça detetada. Os registos estão integrados nos Registos do Diagnóstico do Azure e os alertas são registados em formato json.

![imageURLroute](./media/application-gateway-webapplicationfirewall-overview/waf2.png)

```json
{
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<applicationGatewayName>",
    "operationName": "ApplicationGatewayFirewall",
    "time": "2016-09-20T00:40:04.9138513Z",
    "category": "ApplicationGatewayFirewallLog",
    "properties":     {
        "instanceId":"ApplicationGatewayRole_IN_0",
        "clientIp":"108.41.16.164",
        "clientPort":1815,
        "requestUri":"/wavsep/active/RXSS-Detection-Evaluation-POST/",
        "ruleId":"OWASP_973336",
        "message":"XSS Filter - Category 1: Script Tag Vector",
        "action":"Logged",
        "site":"Global",
        "message":"XSS Filter - Category 1: Script Tag Vector",
        "details":{"message":" Warning. Pattern match "(?i)(<script","file":"/owasp_crs/base_rules/modsecurity_crs_41_xss_attacks.conf","line":"14"}}
}
```

## <a name="application-gateway-waf-sku-pricing"></a>Preços da SKU da WAF do Gateway de Aplicação

Durante a pré-visualização, a utilização da WAF do Gateway de Aplicação não implica custos adicionais. Continuam-lhe a ser cobradas as taxas existentes do SKU Básico. As taxas do SKU da WAF serão comunicadas no momento da Disponibilidade Geral. Os clientes que tenham optado por implementar o Gateway de Aplicação no SKU da WAF apenas começariam a acumular os preços do SKU da WAF apenas após o anúncio de Disponibilidade Geral.

## <a name="next-steps"></a>Passos seguintes

Após saber mais sobre as capacidades da WAF, visite [Como configurar a Firewall de Aplicações Web no Gateway de Aplicação](application-gateway-web-application-firewall-portal.md).




<!--HONumber=Dec16_HO5-->



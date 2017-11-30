---
title: "Perguntas mais frequentes sobre o Gateway de aplicação do Azure | Microsoft Docs"
description: "Esta página fornece respostas às perguntas mais frequentes sobre o Gateway de aplicação do Azure"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: d54ee7ec-4d6b-4db7-8a17-6513fda7e392
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: davidmu
ms.openlocfilehash: f92af44df9863bbf48abb4afcf9b1505c843fadc
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Perguntas mais frequentes sobre o Gateway de aplicação

## <a name="general"></a>Geral

**Q. O que é o Gateway de aplicação?**

Gateway de aplicação do Azure é um controlador de entrega de aplicações (ADC) como um serviço, oferecendo capacidades para as suas aplicações de balanceamento de carga de camada 7 vários. Serviço de elevada disponibilidade e dimensionável, que é completamente gerido pelo Azure oferece.

**Q. Quais as funcionalidades que suporta o Gateway de aplicação?**

Gateway de aplicação suporta a descarga de SSL e SSL ponto a ponto, Firewall de aplicações Web, afinidade com base no cookie de sessão, url com base no caminho encaminhamento, transmissões de alojamento de site e outras pessoas. Para obter uma lista completa das funcionalidades suportadas, aceda [introdução ao Gateway de aplicação](application-gateway-introduction.md)

**Q. Qual é a diferença entre o Gateway de aplicação e o Balanceador de carga do Azure?**

Gateway de aplicação é um balanceador de carga de camada 7, o que significa que funciona com apenas tráfego web (HTTP/HTTPS/WebSocket). Suporta capacidades, tal como a terminação de SSL, afinidade de sessão com base em cookies e o round robin para tráfego de balanceamento de carga. O Balanceador de carga, a carga do tráfego de saldos na camada 4 (TCP/UDP).

**Q. Os protocolos suporta o Gateway de aplicação?**

Gateway de aplicação suporta HTTP, HTTPS e WebSocket.

**Q. Os recursos que são suportados atualmente como parte do conjunto back-end?**

Conjuntos de back-end podem ser compostos por NICs, conjuntos de dimensionamento de máquina virtual, os IPs públicos, os nomes de IPs interna, de domínio completamente qualificado (FQDN) e back-ends de multi-inquilino, como as aplicações Web do Azure. Membros do agrupamento de back-end de Gateway de aplicação não estão associados a um conjunto de disponibilidade. Os membros dos conjuntos de back-end podem ser entre clusters, centros de dados, ou fora do Azure, desde que têm conectividade IP.

**Q. As regiões está disponível no serviço?**

Gateway de aplicação está disponível em todas as regiões do global Azure. Também está disponível no [Azure China](https://www.azure.cn/) e [Azure Government](https://azure.microsoft.com/en-us/overview/clouds/government/)

**Q. Esta é uma implementação dedicada para a minha subscrição ou é partilhado entre os clientes?**

Gateway de aplicação é uma implementação dedicada na sua rede virtual.

**Q. É HTTP -> redirecionamento HTTPS suportado?**

Redirecionamento é suportado. Visite [descrição geral do redirecionamento de Gateway de aplicação](application-gateway-redirect-overview.md) para obter mais informações.

**Q. Ordem pela qual os serviços de escuta processados?**

Os serviços de escuta são processados pela ordem que são apresentadas. Por que motivo se um pedido recebido corresponde a um serviço de escuta básico processa-lo primeiro.  Serviços de escuta de vários sites devem ser configurados antes de um serviço de escuta básico para garantir que o tráfego é encaminhado para o back-end correto.

**Q. Onde encontrar DNS e de IP do Gateway de aplicação?**

Ao utilizar um endereço IP público como um ponto final, estes pode encontrar informações no recurso de endereço IP público ou na página de descrição geral para o Gateway de aplicação no portal. Para endereços IP internos, isto pode ser encontrado na página de descrição geral.

**Q. O IP ou o DNS alteram ao longo da duração do Gateway de aplicação?**

O VIP pode alterar se o gateway está parado e iniciado pelo cliente. O DNS associado com Gateway de aplicação não alteram ao longo do ciclo de vida do gateway. Por este motivo, é recomendado utilizar um alias CNAME e apontar para o endereço DNS do Gateway de aplicação.

**Q. Gateway de aplicação suporta o IP estático?**

Não, o Gateway de aplicação não suporta os endereços IP públicos estáticos, mas suporta IPs internos estáticos.

**Q. Gateway de aplicação suporta vários IPs públicos no gateway?**

Apenas um endereço IP público é suportado um Gateway de aplicação.

**Q. Gateway de aplicação suporta x-reencaminhados-para cabeçalhos?**

Sim, o Gateway de aplicação insere cabeçalhos x-reencaminhados-de, proto x reencaminhados e porta x reencaminhados para o pedido reencaminhado para o back-end. O formato de cabeçalho x-reencaminhados-para-se uma lista separada por vírgulas de IP:Port. Os valores válidos para proto x reencaminhados são http ou https. Porta X reencaminhados Especifica a porta à qual o pedido atingiu o Gateway de aplicação.

**Q. Quanto tempo demora para implementar um Gateway de aplicação? O meu Gateway de aplicação ainda funciona quando a ser atualizado?**

Novas implementações de Gateway de aplicação podem demorar até 20 minutos para aprovisionar. As alterações ao tamanho/contagem de instâncias não são acontece e o gateway permanece ativo durante este período.

## <a name="configuration"></a>Configuração

**Q. Gateway de aplicação é implementada sempre numa rede virtual?**

Sim, o Gateway de aplicação sempre é implementado numa sub-rede de rede virtual. Esta sub-rede só pode conter os Gateways de aplicação.

**Q. Gateway de aplicação pode falar para instâncias fora da sua rede virtual?**

Gateway de aplicação pode comunicar com instâncias fora da rede virtual que está no desde há conetividade IP. Se planeia utilizar IPs interno como membros do conjunto de back-end, em seguida, requer [VNET Peering](../virtual-network/virtual-network-peering-overview.md) ou [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

**Q. Posso implementar tudo o resto da sub-rede do Gateway de aplicação?**

Não, mas pode implementar outros gateways de aplicação na sub-rede.

**Q. Grupos de segurança de rede são suportados na sub-rede de Gateway de aplicação?**

Grupos de segurança de rede são suportados na sub-rede de Gateway de aplicação com as seguintes restrições:

* Exceções devem ser colocadas para o tráfego de entrada nas portas 65503-65534 para Estado de funcionamento de back-end funcionar corretamente.

* Conectividade de internet de saída não pode ser bloqueada.

* Tráfego de etiqueta AzureLoadBalancer tem de ser permitido.

**Q. Quais são os limites no Gateway de aplicação? Pode aumentar estes limites?**

Visite [limites de Gateway de aplicação](../azure-subscription-service-limits.md#application-gateway-limits) para ver os limites.

**Q. Posso utilizar o Gateway de aplicação para o tráfego externo e interno em simultâneo?**

Sim, o Gateway de aplicação suporta ter um IP interno e um IP externo por Gateway de aplicação.

**Q. O VNet peering suportado?**

Sim, o VNet peering é suportado e é vantajoso para o tráfego em outras redes virtuais de balanceamento de carga.

**Q. Posso falar para servidores no local quando estão ligados por túneis ExpressRoute ou VPN?**

Sim, desde que o tráfego é permitido.

**Q. Pode ter um conjunto de back-end que serve muitas aplicações nas portas diferentes?**

Arquitetura do serviço micro é suportada. Terá de várias definições de http configuradas para pesquisa em portas diferentes.

**Q. Das sondas personalizadas suportam carateres universais/regex nos dados de resposta?**

Das sondas personalizadas não suportam carateres universais ou regex nos dados de resposta. 

**Q. Como as regras são processadas?**

As regras são processadas pela ordem que estão configurados. Recomenda-se que as regras de vários sites estão configuradas antes basic regras para reduzir as hipóteses desse tráfego é encaminhado para o back-end inadequado como a regra básica corresponderia tráfego com base na porta antes da regra de vários site que está a ser avaliada.

**Q. Como as regras são processadas?**

As regras são processadas pela ordem que são criados. Recomenda-se que as regras de multilocal estão configuradas antes das regras básicas. Ao configurar os serviços de escuta de vários sites pela primeira vez, esta configuração reduz a probabilidade que o tráfego é encaminhado para o back-end inadequado. Este problema de encaminhamento pode ocorrer porque a regra básica corresponderia tráfego com base na porta antes da regra de vários site que está a ser avaliada.

**Q. O que significam o campo de anfitrião das sondas personalizadas?**

Campo anfitrião Especifica o nome para enviar a sonda a. Aplicável apenas quando vários sites está configurada no Gateway de aplicação, caso contrário, utilize '127.0.0.1'. Este valor é diferente do nome de anfitrião VM e está no formato \<protocolo\>://\<anfitrião\>:\<porta\>\<caminho\>.

**Q. Posso lista branca de acesso de Gateway de aplicação à origem alguns IPs?**

Este cenário pode ser feito utilizando NSGs na sub-rede de Gateway de aplicação. As seguintes restrições devem ser colocadas na sub-rede listados pela ordem de prioridade em:

* Permitir o tráfego de entrada do intervalo de IP/IP de origem.

* Permitir pedidos de entrada de todas as origens para portas 65503 65534 para [comunicação de estado de funcionamento de back-end](application-gateway-diagnostics.md).

* Permitir entradas sondas de Balanceador de carga do Azure (etiqueta AzureLoadBalancer) e a entrada tráfego de rede virtual (etiqueta VirtualNetwork) no [NSG](../virtual-network/virtual-networks-nsg.md).

* Bloquear todas as regras de todos os outros tráfego de entrada com uma negação.

* Permitir o tráfego de saída para a internet para todos os destinos.

## <a name="performance"></a>Desempenho

**Q. Como a que o Gateway de aplicação suporta elevada disponibilidade e escalabilidade?**

Gateway de aplicação suporta cenários de elevada disponibilidade, se tiver duas ou mais instâncias implementadas. Azure distribui estes instâncias de domínios de atualização e falhas para se certificar de que todas as instâncias não falhar ao mesmo tempo. Gateway de aplicação suporta escalabilidade ao adicionar várias instâncias do mesmo gateway para partilhar a carga.

**Q. Como obter cenário de DR a em todos os centros de dados com o Gateway de aplicação?**

Os clientes podem utilizar o Gestor de tráfego para distribuir tráfego por vários Gateways de aplicação em datacenters diferentes.

**Q. Escala automática suportada?**

Não, mas o aplicação Gateway tem uma métrica de débito que pode ser utilizada para o alertar quando for atingido um limiar. Manualmente adicionar instâncias ou alterar o tamanho não reinicie o gateway e não afeta a tráfego existente.

**Q. É manual escala cima/para baixo causa período de indisponibilidade?**

Sem períodos de indisponibilidade, existe instâncias estão distribuídas por domínios de atualização e domínios de falhas.

**Q. Posso alterar tamanho da instância de média a grande sem interrupção?**

Sim, o Azure distribui as instâncias em vários domínios de atualização e falhas para se certificar de que todas as instâncias não falhar ao mesmo tempo. Gateway de aplicação suporta o dimensionamento, adicionar várias instâncias do mesmo gateway para partilhar a carga.

## <a name="ssl-configuration"></a>Configuração de SSL

**Q. Os certificados são suportados no Gateway de aplicação?**

Self-assinado certificados, certificados de AC, e certificados de caráter universal são suportados. EV certificados não são suportados.

**Q. Quais são os conjuntos de cifras atual suportados pelo Gateway de aplicação?**

Seguem-se os conjuntos de cifras atual suportados pelo gateway de aplicação. Visite: [configurar SSL versões de política e conjuntos de cifras no Gateway de aplicação](application-gateway-configure-ssl-policy-powershell.md) para saber como personalizar as opções de SSL.

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

**Q. Gateway de aplicação também suporta a encriptação de tráfego para o back-end?**

Sim, o Gateway de aplicação suporta descarga de SSL e o SSL de ponto a ponto, que encripta o tráfego para o back-end novamente.

**Q. Pode configurar política SSL para controlar as versões de protocolo SSL?**

Sim, pode configurar o Gateway de aplicação para negar TLS1.0, TLS1.1 e TLS1.2. SSL 2.0 e 3.0 já estão desativados por predefinição e não são configuráveis.

**Q. Pode configurar conjuntos de cifras e ordem de política?**

Sim, [configuração de conjuntos de cifras](application-gateway-ssl-policy-overview.md) é suportada. Ao definir uma política personalizada, pelo menos um dos conjuntos de cifras seguintes tem de estar ativado. Gateway de aplicação utiliza SHA256 para gestão de back-end.

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

**Q. Quantos certificados SSL são suportados?**

Até 20 SSL certificados são suportados.

**Q. Quantos certificados de autenticação de back-end a reencriptação são suportados?**

Até 10 autenticação os certificados são suportados com uma predefinição de 5.

**Q. Gateway de aplicação integrar com o Cofre de chaves do Azure nativamente?**

Não, não estiver integrado com o Cofre de chaves do Azure.

## <a name="web-application-firewall-waf-configuration"></a>Configuração de Firewall (WAF) de aplicação Web

**Q. O SKU de WAF oferta de todas as funcionalidades disponíveis com o SKU Standard?**

Sim, WAF suporta todas as funcionalidades de SKU Standard.

**Q. O que é a versão de CR Gateway de aplicação suporta?**

Gateway de aplicação suporta CR [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) e CR [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

**Q. Como monitorizo WAF?**

WAF está a ser monitorizado através do registo de diagnóstico, obter mais informações sobre o registo de diagnóstico podem ser encontradas em [registo de diagnóstico e métricas de Gateway de aplicação](application-gateway-diagnostics.md)

**Q. Modo de deteção bloquear o tráfego?**

Não, o modo de deteção apenas os registos de tráfego, que é acionada uma regra de WAF.

**Q. Como posso personalizar regras WAF?**

Sim, as regras de WAF são personalizáveis, para obter mais informações sobre como personalizá-los visite [grupos de regras de personalizar WAF e regras](application-gateway-customize-waf-rules-portal.md)

**Q. As regras estão atualmente disponíveis?**

Atualmente, o WAF suporta CR [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) e [3.0](application-gateway-crs-rulegroups-rules.md#owasp30), que fornecem segurança de linha de base contra a maioria das vulnerabilidades 10 principais identificadas pelo abra Web aplicação segurança projeto (OWASP) encontrada aqui [ OWASP 10 principais vulnerabilidades](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)

* Proteção contra injeção de SQL

* Proteção contra scripting entre sites

* Proteção contra Ataques Web comuns, como, por exemplo, injeção de comandos, contrabando de pedidos HTTP, divisão de respostas HTTP e ataques remotos de inclusão de ficheiros

* Proteção contra violações de protocolo HTTP

* Proteção contra anomalias de protocolo HTTP, como agente de utilizador de anfitrião e cabeçalhos de aceitação em falta

* Prevenção de contra bots, crawlers e scanners

 * Deteção de aplicação configurações incorretas comuns (ou seja, o Apache, IIS, etc.)

**Q. WAF também suporta DDoS prevenção?**

Não, WAF não fornece DDoS prevenção.

## <a name="diagnostics-and-logging"></a>Diagnóstico e de registo

**Q. Que tipos de registos estão disponíveis com o Gateway de aplicação?**

Existem três registos disponíveis para o Gateway de aplicação. Para obter mais informações sobre estes registos e outras funcionalidades de diagnóstico, visite [back-end estado de funcionamento, registos de diagnóstico e métricas de Gateway de aplicação](application-gateway-diagnostics.md).

- **ApplicationGatewayAccessLog** -o registo de acesso contém cada pedido submetido para o front-end do Gateway de aplicação. Os dados incluem IP o chamador, o URL pedido, latência de resposta, devolver o código, bytes e terminar. Registo de acesso é recolhido a cada 300 segundos. Este registo contém um registo por instância de Gateway de aplicação.
- **ApplicationGatewayPerformanceLog** -o registo de desempenho captura informações de desempenho per instância com base num incluindo pedido total servido, débito em bytes, total de pedidos servidos, contagem de pedidos falhados, back-end de bom estado de funcionamento e mau estado de funcionamento Contagem de instâncias.
- **ApplicationGatewayFirewallLog** -o registo de firewall contém pedidos que são registados através do modo de deteção ou prevenção de um gateway de aplicação que está configurado com firewall de aplicações web.

**Q. Como saber se a minha membros do conjunto de back-end estão em bom Estados?**

Pode utilizar o cmdlet do PowerShell `Get-AzureRmApplicationGatewayBackendHealth` ou verificar o estado de funcionamento através do portal, visitando [diagnóstico do Gateway de aplicação](application-gateway-diagnostics.md)

**Q. O que é a política de retenção os registos de diagnóstico?**

Fluxo de registos de diagnóstico para a conta de armazenamento de clientes e os clientes podem definir a política de retenção com base na sua preferência. Os registos de diagnóstico podem também ser enviados para um Hub de eventos ou análise de registos. Visite [diagnóstico do Gateway de aplicação](application-gateway-diagnostics.md) para obter mais detalhes.

**Q. Como posso obter registos de auditoria para o Gateway de aplicação?**

Os registos de auditoria estão disponíveis para o Gateway de aplicação. No portal, clique em **registo de atividade** no painel do menu de um Gateway de aplicação para aceder ao registo de auditoria. 

**Q. Pode definir alertas com Gateway de aplicação?**

Sim, Gateway de aplicação suportar alertas, alertas são configuradas desativar métricas.  Gateway de aplicação não tem atualmente uma métrica de "débito", que pode ser configurado para o alerta. Para obter mais informações sobre alertas, visite [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

**Q. Estado de funcionamento de back-end devolve Estado desconhecido, que pode causar este Estado?**

A razão mais comum é acesso para o back-end está a ser bloqueado por um NSG ou DNS personalizado. Visite [back-end estado de funcionamento, o registo de diagnóstico e métricas de Gateway de aplicação](application-gateway-diagnostics.md) para obter mais informações.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a visita de Gateway de aplicação [introdução ao Gateway de aplicação](application-gateway-introduction.md).

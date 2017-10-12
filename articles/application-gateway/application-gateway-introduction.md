---
title: "Introdução ao Gateway de Aplicação do Azure| Microsoft Docs"
description: "Esta página oferece uma descrição geral sobre o serviço de Gateway de Aplicação para o balanceamento de carga de camada 7, incluindo tamanhos de gateway, balanceamento de carga HTTP, afinidade de sessão baseada no cookie e descarga de SSL."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: b37a2473-4f0e-496b-95e7-c0594e96f83e
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: davidmu
ms.openlocfilehash: 33968b72d0da71577428937e5d293a40d62989f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-application-gateway"></a>Descrição geral do Gateway de Aplicação

O Gateway de Aplicação do Microsoft Azure é uma aplicação virtual dedicada, que disponibiliza um controlador de entrega de aplicação (ADC) como um serviço. Oferece várias capacidades de balanceamento de carga de camada 7 para a sua aplicação. Permite que os clientes otimizem a produtividade do Web farm ao descarregar a terminação de SSL com utilização intensiva da CPU para o gateway de aplicação. Proporciona também outras capacidades de encaminhamento de camada 7, incluindo a distribuição round robin de tráfego de entrada, a afinidade de sessão com base em cookies, o encaminhamento baseado no caminho do URL e a capacidade de alojar vários Web sites atrás de um Gateway de Aplicação individual. Também é fornecida uma firewall de aplicações Web (WAF) como parte do SKU da WAF do gateway de aplicação. Fornece proteção para as aplicações Web contra vulnerabilidades e explorações Web comuns. O Gateway de Aplicação pode ser configurado como um gateway com acesso à Internet, gateway só interno ou uma combinação de ambos. 

![cenário](./media/application-gateway-introduction/scenario.png)

## <a name="features"></a>Funcionalidades

O Gateway de Aplicação, atualmente, fornece as seguintes capacidades:


* **[Firewall de aplicações web](application-gateway-webapplicationfirewall-overview.md)** - a firewall de aplicações Web (WAF) no Gateway de Aplicação do Azure protege aplicações Web contra ataques baseados na Web comuns, como injeção SQL, ataques de scripts entre sites e assunção do controlo de sessão sem autorização.
* **Balanceamento de carga HTTP** - O Gateway de Aplicação fornece a opção de balanceamento de carga round robin. O balanceamento de carga é feito na Camada 7 e serve apenas para tráfego HTTP(S).
* **Afinidade de sessão com base no cookie** - Esta funcionalidade é útil quando quiser manter uma sessão de utilizador no mesmo back-end. Ao utilizar cookies geridos por gateway, o Gateway de Aplicação pode direcionar o tráfego subsequente de uma sessão de utilizador para o mesmo back-end para processamento. Esta funcionalidade é importante em casos em que o estado da sessão é guardado localmente no servidor de back-end para uma sessão de utilizador.
* **[Descarga do SSL (Secure Sockets Layer)](application-gateway-ssl-arm.md)** - Esta funcionalidade elimina a tarefa dispendiosa de desencriptação de tráfego HTTPS para os servidores Web. Ao terminar a ligação SSL no Gateway de Aplicação e reencaminhar o pedido para o servidor não encriptado, o servidor Web é aliviado pela desencriptação.  O Gateway de Aplicação encripta novamente a resposta antes de a enviar para o cliente. Esta funcionalidade é útil em cenários onde o back-end está localizado na mesma rede virtual protegida que o Gateway de Aplicação no Azure.
* **[SSL de Ponta a Ponta](application-gateway-backend-ssl.md)** - O Gateway de Aplicação suporta a encriptação de ponta a ponta do tráfego. O Gateway de Aplicação realiza esta ação ao terminar a ligação SSL no gateway de aplicação. O gateway, em seguida, aplica as regras de encaminhamento para o tráfego, encripta novamente o pacote e reencaminha o pacote para o back-end adequado, com base nas regras de encaminhamento definidas. Qualquer resposta do servidor Web atravessa o mesmo processo para o utilizador final.
* **[Encaminhamento de conteúdo baseado em URL](application-gateway-url-route-overview.md)** - Esta funcionalidade fornece a capacidade para utilizar diferentes servidores de back-end para tráfego diferentes. O tráfego para uma pasta no servidor Web ou uma CDN pode ser encaminhado para um back-end diferente. Esta capacidade reduz a carga desnecessária nos back-ends que não servem conteúdo específico.
* **[Encaminhamento multilocal](application-gateway-multi-site-overview.md)** - O gateway de aplicação permite-lhe consolidar até 20 Web sites num gateway de aplicação único.
* **[Suporte de Websocket](application-gateway-websocket.md)** - Outra funcionalidade excelente de Gateway de Aplicação é o suporte nativo para Websocket.
* **[Monitorização de estado de funcionamento](application-gateway-probe-overview.md)** - O gateway de aplicação fornece monitorização do estado de funcionamento predefinido e sondas personalizadas para monitorizar cenários mais específicos.
* **[Política de SSL e Cifras](application-gateway-ssl-policy-overview.md)** - Esta funcionalidade fornece a capacidade de limitar as versões de protocolo SSL e os conjuntos de cifras que são suportados e a ordem na qual são processados.
* **[Redirecionamento de pedidos](application-gateway-redirect-overview.md)** - esta funcionalidade proporciona a capacidade de redirecionar pedidos HTTP para um serviço de escuta de HTTPS.
* **[Suporte de back-end multi-inquilino](application-gateway-web-app-overview.md)**  - O Gateway de Aplicação suporta a configuração de serviços de back-end multi-inquilino, como as Aplicações Web do Azure e o Gateway da API, como membros do conjunto de back-end. 
* **[Diagnóstico avançado](application-gateway-diagnostics.md)** - o gateway de aplicação fornece o diagnóstico completo e registos de acesso. Os registos de firewall estão disponíveis para recursos de gateway de aplicações que tenham a WAF ativada.

## <a name="benefits"></a>Benefícios

O Gateway de Aplicação é útil para:

* As aplicações que requerem pedidos a partir da mesma sessão de utilizador/cliente para alcançarem a mesma máquina virtual de back-end. Exemplos destas aplicações seriam aplicações de carrinhos de compras e servidores de Webmail.
* Remove a sobrecarga de terminação SSL para farms de servidores Web.
* Aplicações, como uma rede de entrega de conteúdos, que requerem vários pedidos HTTP na mesma ligação TCP de execução longa a serem encaminhadas ou terem a carga balanceada para diferentes servidores de back-end.
* Aplicações que suportam tráfego do websocket
* Proteger aplicações Web de ataques baseados na web comuns, como injeção SQL, ataques de scripts entre sites e assunção de controlo de sessão sem autorização.
* Distribuição lógica de tráfego de com base nos critérios de encaminhamento diferentes, tais como a caminho do url ou os cabeçalhos de domínio.

O Gateway de Aplicação é totalmente gerido pelo Azure, dimensionável e tem uma disponibilidade elevada. Proporciona um conjunto avançado de capacidades de registo e diagnóstico, para uma melhor capacidade de gestão. Quando cria um gateway de aplicação, um ponto final (VIP público ou ILB IP interno) está associado e serve como tráfego de rede de entrada. Este VIP ou ILP IP é proporcionado pelo Balanceador de Carga do Azure a funcionar ao nível do transporte (TCP/UDP), tendo todo o tráfego de rede de entrada com a carga balanceada para as instâncias de trabalho do gateway de aplicação. Em seguida, o gateway de aplicação encaminha o tráfego HTTP/HTTPS com base na respetiva configuração, seja uma máquina virtual, um serviço cloud ou um endereço IP externo.

O balanceamento de carga do Gateway de Aplicação como um serviço gerido pelo Azure permite o aprovisionamento de um balanceador de carga de camada 7, atrás do balanceador de carga de software do Azure. O gestor de tráfego pode ser utilizado para concluir o cenário, conforme se verifica na imagem seguinte, onde o Gestor de Tráfego fornece redirecionamento e disponibilidade de tráfego a vários recursos de gateway de aplicação em diferentes regiões, e o gateway de aplicação fornece balanceamento de carga de camada 7 de região cruzada. Pode ver um exemplo deste cenário em: [Using load balancing services in the Azure cloud (Utilizar serviços de balanceamento de carga na nuvem do Azure)](../traffic-manager/traffic-manager-load-balancing-azure.md)

![cenário do gestor de tráfego e gateway de aplicação](./media/application-gateway-introduction/tm-lb-ag-scenario.png)

[!INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="gateway-sizes-and-instances"></a>Instâncias e tamanhos de gateway

O Gateway de Aplicação é atualmente oferecido em três tamanhos: **Pequeno**, **Médio** e **Grande**. Os tamanhos de instâncias pequenas destinam-se a cenários de testes e desenvolvimento.

Pode criar até 50 gateways de aplicação por subscrição e cada gateway de aplicação pode ter até 10 instâncias. Cada gateway de aplicação pode consistir em 20 serviços de escuta http. Para obter uma lista completa dos limites do gateway de aplicação, veja [limites do serviço Gateway de Aplicação](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

A tabela seguinte mostra um débito de desempenho médio para cada instância de gateway de aplicação com descarga de SSL ativada:

| Resposta de página de back-end | Pequeno | Médio | Grande |
| --- | --- | --- | --- |
| 6K |7.5 Mbps |13 Mbps |50 Mbps |
| 100K |35 Mbps |100 Mbps |200 Mbps |

> [!NOTE]
> Estes valores são valores aproximados para um débito de gateway de aplicação. O débito real depende de vários detalhes de ambiente, como o tamanho médio da página, a localização das instâncias de back-end e o tempo de processamento para servir uma página. Para números de desempenho exatos, deve executar o seus próprios testes. Estes valores são fornecidos apenas para a capacidade orientação de planeamento.

## <a name="health-monitoring"></a>Monitorização do estado de funcionamento

O Gateway de Aplicação do Azure monitoriza automaticamente o estado de funcionamento das instâncias de back-end através de sondas de estado de funcionamento personalizado ou básico. Ao utilizar sondas de estado de funcionamento, isto garante que apenas bom anfitriões respondam ao tráfego. Para obter mais informações, consulte [Application Gateway health monitoring overview (Descrição geral da monitorização do estado de funcionamento do Gateway de Aplicação)](application-gateway-probe-overview.md).

## <a name="configuring-and-managing"></a>Configurar e gerir

Para o ponto final, o gateway de aplicação pode ter um IP público, um IP privado ou ambos, quando está configurado. O Gateway de Aplicação é configurado no interior de uma rede virtual na sua própria sub-rede. A sub-rede criada ou utilizada para o gateway de aplicação não pode conter quaisquer outros tipos de recursos, os únicos recursos que são permitidos na sub-rede são outros gateways de aplicação. Para proteger os recursos de back-end, os servidores de back-end podem ser contidos numa sub-rede diferente na mesma rede virtual que o gateway de aplicação. Esta sub-rede não é necessária para as aplicações de back-end. Desde que o gateway de aplicação possa alcançar o endereço IP, o gateway de aplicação pode fornecer capacidades de ADC para os servidores de back-end. 

Pode criar e gerir um gateway de aplicação com APIs REST, cmdlets do PowerShell, CLI do Azure ou [portal do Azure](https://portal.azure.com/). Para ver perguntas adicionais sobre o Gateway de Aplicação, visite [Application Gateway FAQ](application-gateway-faq.md) (FAQ do Gateway de Aplicação) para obter uma lista das perguntas mais frequentes.

## <a name="pricing"></a>Preços

O preço baseia-se no custo de instância de gateway por hora e no custo do processamento de dados. Os preços dos gateways por hora para o SKU da WAF são diferentes dos custos do SKU Standard. Estas informações de preços podem ser encontradas nos [Detalhes de preços do Gateway de Aplicação](https://azure.microsoft.com/pricing/details/application-gateway/). Os custos de processamento permanecem iguais.

## <a name="faq"></a>FAQ

Para perguntas mais frequentes sobre o Gateway de Aplicação, consulte [Application Gateway FAQ (FAQ sobre o Gateway de Aplicação)](application-gateway-faq.md).

## <a name="next-steps"></a>Passos seguintes

Após aprender sobre o Gateway de aplicação, pode [criar um gateway de aplicação](application-gateway-create-gateway-portal.md) ou [criar uma descarga SSL de gateway de aplicação](application-gateway-ssl-arm.md) para ligações de HTTPS com carga balanceada.

Para saber como criar um gateway de aplicação com encaminhamento de conteúdo baseado em URL, aceda a [Create an application gateway using URL-based routing (Criar um gateway de aplicação com encaminhamento baseado em URL)](application-gateway-create-url-route-arm-ps.md) para obter mais informações.

Para saber mais sobre algumas das outras capacidades de rede principais do Azure, veja [Rede Azure](../networking/networking-overview.md).

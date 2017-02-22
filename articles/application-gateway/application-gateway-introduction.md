---
title: "Introdução ao Gateway de Aplicação | Microsoft Docs"
description: "Esta página oferece uma descrição geral sobre o serviço de Gateway de Aplicação para o balanceamento de carga de camada 7, incluindo tamanhos de gateway, balanceamento de carga HTTP, afinidade de sessão baseada no cookie e descarga de SSL."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: b37a2473-4f0e-496b-95e7-c0594e96f83e
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: ca87ad0165c7333fd43a828f7d92d46a432d8f78
ms.openlocfilehash: 6956353771e6b4bcb814eb7cc9dfde01c452b8bd


---
# <a name="application-gateway-overview"></a>Descrição geral do Gateway de Aplicação

## <a name="what-is-application-gateway"></a>O que é o Gateway de Aplicação

O Gateway de Aplicação do Microsoft Azure fornece um Controlador de Entrega de Aplicação (ADC) como um serviço e oferece várias capacidades de balanceamento de carga de camada 7 para a sua aplicação. Permite que os clientes otimizem a produtividade do Web farm ao descarregar a terminação de SSL com utilização intensiva da CPU para o Gateway de Aplicação. Proporciona também outras capacidades de encaminhamento de Camada 7, incluindo a distribuição round robin de tráfego de entrada, a afinidade de sessão com base no cookie, o encaminhamento baseado no caminho do URL e a capacidade de alojar vários Web sites atrás de um único Gateway de Aplicação. O Gateway de Aplicação também tem uma firewall de aplicações Web (WAF) que protege a aplicação contra a maior parte das 10 vulnerabilidades mais comuns na Web da OWASP. O Gateway de Aplicação pode ser configurado como um gateway com acesso à Internet, gateway só interno ou uma combinação de ambos. O Gateway de Aplicação é totalmente gerido pelo Azure, dimensionável e tem uma disponibilidade elevada. Proporciona um conjunto avançado de capacidades de registo e diagnóstico para uma melhor capacidade de gestão. O gateway de aplicação funciona com máquinas virtuais, serviços em nuvem e aplicações Web com acesso ao interior ou ao exterior.

O Gateway de Aplicação é um dispositivo virtual dedicado para a sua aplicação e é composto por várias instâncias de trabalho para escalabilidade e elevada disponibilidade. Quando cria um gateway de aplicação, um ponto final (VIP público ou ILB IP interno) está associado e serve como tráfego de rede de entrada. Este VIP ou ILP IP é proporcionado pelo Balanceador de Carga do Azure a funcionar ao nível do transporte (TCP/UDP), tendo todo o tráfego de rede de entrada com a carga balanceada para as instâncias de trabalho do Gateway de Aplicação. Em seguida, o Gateway de Aplicação encaminha o tráfego HTTP/HTTPS com base na respetiva configuração, seja uma máquina virtual, um serviço em nuvem ou um endereço IP externo. Para o SLA e preços, consulte as páginas [SLA](https://azure.microsoft.com/support/legal/sla/) e [Pricing (Preços)](https://azure.microsoft.com/pricing/details/application-gateway/).

## <a name="features"></a>Funcionalidades

O Gateway de Aplicação suporta atualmente a entrega da aplicação de camada 7 com as seguintes funcionalidades:

* **[Firewall da Aplicação Web (Pré-visualização)](application-gateway-webapplicationfirewall-overview.md)** - A firewall da aplicação Web (WAF) no Gateway de Aplicação do Azure protege aplicações Web contra ataques baseados na web comuns, como injeção SQL, ataques de scripts entre sites e assunção do controlo de sessão sem autorização.
* **Balanceamento de carga HTTP** - O Gateway de Aplicação fornece a opção de balanceamento de carga round robin. O balanceamento de carga é feito na Camada 7 e serve apenas para tráfego HTTP(S).
* **Afinidade de sessão com base no cookie** - Esta funcionalidade é útil quando pretender manter uma sessão de utilizador no mesmo back-end. Ao utilizar cookies geridos por gateway, o Gateway de Aplicação pode direcionar tráfego subsequente de uma sessão de utilizador para o mesmo back-end para processamento. Esta funcionalidade é importante em casos em que o estado da sessão é guardado localmente no servidor de back-end para uma sessão de utilizador.
* **[Descarga do SSL (Secure Sockets Layer)](application-gateway-ssl-arm.md)** - Esta funcionalidade elimina a tarefa dispendiosa de desencriptação de tráfego HTTPS para os servidores Web. Ao terminar a ligação SSL no Gateway de Aplicação e reencaminhar o pedido para o servidor não encriptado, o servidor Web é aliviado pela desencriptação.  O Gateway de Aplicação encripta novamente a resposta antes de a enviar para o cliente. Esta funcionalidade é útil em cenários onde o back-end está localizado na mesma rede virtual protegida que o Gateway de Aplicação no Azure.
* **[SSL de Ponta a Ponta](application-gateway-backend-ssl.md)** - O Gateway de Aplicação suporta a encriptação de ponta a ponta do tráfego. O Gateway de Aplicação realiza esta ação ao terminar a ligação SSL no gateway de aplicação. O gateway, em seguida, aplica as regras de encaminhamento para o tráfego, encripta novamente o pacote e reencaminha o pacote para o back-end adequado, com base nas regras de encaminhamento definidas. Qualquer resposta do servidor Web atravessa o mesmo processo para o utilizador final.
* **[Encaminhamento de conteúdo baseado em URL](application-gateway-url-route-overview.md)** - Esta funcionalidade fornece a capacidade para utilizar diferentes servidores de back-end para tráfego diferentes. O tráfego de uma pasta no servidor Web ou de um CDN poderia ser encaminhado para um back-end diferente, reduzindo a carga desnecessária nos back-ends que não atendem conteúdo específico do servidor.
* **[Encaminhamento multilocal](application-gateway-multi-site-overview.md)** - O gateway de aplicação permite-lhe consolidar até 20 Web sites num gateway de aplicação único.
* **[Suporte de Websocket](application-gateway-websocket.md)** - Outra funcionalidade excelente de Gateway de Aplicação é o suporte nativo para Websocket.
* **[Monitorização de estado de funcionamento](application-gateway-probe-overview.md)** - O gateway de aplicação fornece monitorização do estado de funcionamento predefinido e sondas personalizadas para monitorizar cenários mais específicos.
* **[Diagnóstico avançado](application-gateway-diagnostics.md)** - o gateway de aplicação fornece o diagnóstico completo e registos de acesso. Os registos de firewall estão disponíveis para recursos de gateway de aplicações que tenham a WAF ativada.

## <a name="benefits"></a>Benefícios

O Gateway de Aplicação é útil para:

* As aplicações que requerem pedidos a partir da mesma sessão de utilizador/cliente para alcançarem a mesma máquina virtual de back-end. Exemplos destas aplicações seriam aplicações de carrinhos de compras e servidores de correio eletrónico Web.
* As aplicações que pretendem libertar farms de servidores Web de sobrecarga de terminação SSL.
* Aplicações, como uma rede de entrega de conteúdos, que requerem vários pedidos HTTP na mesma ligação TCP de execução longa a serem encaminhadas ou terem a carga balanceada para diferentes servidores de back-end.
* Aplicações que suportam tráfego do websocket
* Proteger aplicações Web de ataques baseados na web comuns, como injeção SQL, ataques de scripts entre sites e assunção de controlo de sessão sem autorização.

O balanceamento de carga do Gateway de Aplicação como um serviço gerido pelo Azure permite o aprovisionamento de um balanceador de carga de camada 7, atrás do balanceador de carga de software do Azure. O gestor de tráfego pode ser utilizado para concluir o cenário, conforme se verifica na imagem seguinte, onde o Gestor de Tráfego fornece redirecionamento e disponibilidade de tráfego a vários recursos de gateway de aplicação em diferentes regiões, e o gateway de aplicação fornece balanceamento de carga de camada 7 de região cruzada. Pode ver um exemplo deste cenário em: [Using load balancing services in the Azure cloud (Utilizar serviços de balanceamento de carga na nuvem do Azure)](../traffic-manager/traffic-manager-load-balancing-azure.md)

![cenário do gestor de tráfego e gateway de aplicação](./media/application-gateway-introduction/tm-lb-ag-scenario.png)

[!INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="gateway-sizes-and-instances"></a>Instâncias e tamanhos de gateway

O Gateway de Aplicação é atualmente oferecido em três tamanhos: **Pequeno**, **Médio** e **Grande**. Os tamanhos de instâncias pequenas destinam-se a cenários de testes e desenvolvimento.

Existem atualmente dois skus para o Gateway de Aplicação: **WAF** e **Standard**.

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

Para o ponto final, o gateway de aplicação pode ter um IP público, um IP privado ou ambos, quando está configurado. O Gateway de Aplicação é configurado no interior de uma rede virtual na sua própria sub-rede. A sub-rede criada ou utilizada para o gateway de aplicação não pode conter quaisquer outros tipos de recursos, os únicos recursos que são permitidos na sub-rede são outros gateways de aplicação. Para proteger os recursos de back-end, os servidores de back-end podem ser contidos numa sub-rede diferente na mesma rede virtual que o gateway de aplicação. Esta sub-rede adicional não é necessária para as aplicações de back-end, desde que o gateway de aplicação possa alcançar o endereço IP, o gateway de aplicação pode fornecer capacidades de ADC para os servidores de back-end.

Pode criar e gerir um gateway de aplicação com APIs REST, cmdlets do PowerShell, CLI do Azure ou [portal do Azure](https://portal.azure.com/).

## <a name="next-steps"></a>Passos seguintes

Após aprender sobre o Gateway de aplicação, pode [criar um gateway de aplicação](application-gateway-create-gateway-portal.md) ou [criar uma descarga SSL de gateway de aplicação](application-gateway-ssl-arm.md) para ligações de HTTPS com carga balanceada.

Para saber como criar um gateway de aplicação com encaminhamento de conteúdo baseado em URL, aceda a [Create an application gateway using URL-based routing (Criar um gateway de aplicação com encaminhamento baseado em URL)](application-gateway-create-url-route-arm-ps.md) para obter mais informações.



<!--HONumber=Jan17_HO5-->



---
title: "Proteger os dados pessoais com funcionalidades de segurança de rede do Azure | Microsoft Docs"
description: "Proteger os dados pessoais utilizar funcionalidades de segurança de rede do Azure"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: d61b29f1327f57bc32b2c53de3fe58e53fcf3cac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="protect-personal-data-with-network-security-features-azure-application-gateway-and-network-security-groups"></a>Proteger os dados pessoais com funcionalidades de segurança de rede: Gateway de aplicação do Azure e os grupos de segurança de rede

Este artigo fornece informações e procedimentos que irão ajudar a utilizam grupos de segurança de rede e Gateway de aplicação do Azure para proteger os dados pessoais.

Um elemento importante de uma estratégia de segurança por várias camadas para proteger a privacidade dos dados pessoais é uma defesa contra exploits de vulnerabilidade comuns, tais como a injeção de SQL ou scripts entre sites. Manter o tráfego de rede indesejado fora do seu Azure rede virtual ajuda a proteger contra a potencial comprometimento de dados confidenciais, e Microsoft Azure fornece ferramentas para ajudar a proteger os seus dados contra atacantes.

## <a name="scenario"></a>Cenário

Uma empresa de grande cruise headquartered nos Estados Unidos, está a expandir as suas operações para oferecer itineraries no Mediterranean, Adriatic e Baltic seas, bem como o Isles território. No furtherance desses esforços, obteve várias linhas cruise mais pequenas com base em (Itália), na Alemanha, Dinamarca e o U.K.

A empresa utiliza o Microsoft Azure para armazenar dados empresariais na nuvem e executar aplicações em máquinas virtuais que processar e aceder a estes dados. Estes dados incluem informações pessoais tais como nomes, endereços, números de telefone e informações de cartão de crédito da respetiva base de cliente global. Também inclui informações de recursos humanos tradicionais, como endereços, números de telefone, os números de identificação de dedução dos impostos e outras informações sobre os funcionários da empresa em todas as localizações. A linha cruise mantém também uma grande base de dados de membros de programa reward e loyalty que inclui informações pessoais, para controlar as relações com clientes atuais e anteriores.

Empresariais aos funcionários acesso à rede de escritórios remotos da empresa e agentes levar localizados em todo o mundo tem acesso a alguns recursos da empresa e utilizar as aplicações baseadas na web alojadas em VMs do Azure para interagir com ele.

## <a name="problem-statement"></a>Declaração do problema

A empresa tem de proteger a privacidade dos clientes e os dados pessoais dos funcionários de atacantes que exploram vulnerabilidades de software para executar código malicioso que pode expor os dados pessoais armazenada ou utilizada por aplicações de baseado na nuvem da empresa.

## <a name="company-goal"></a>Objetivo da empresa

Objetivo da empresa para garantir que as pessoas não autorizadas não é possível aceder empresarial redes virtuais do Azure e as aplicações e dados que residem existe por explorá vulnerabilidades comuns. 

## <a name="solutions"></a>Soluções

O Microsoft Azure oferece mecanismos de segurança para ajudar a impedir que o tráfego indesejável introduzir redes virtuais do Azure. Controlo de tráfego de entrada e saído tradicionalmente é efetuado por firewalls. No Azure, pode utilizar o Gateway de aplicação com a Firewall de aplicações Web e grupos de segurança de rede (NSG), que agem como uma firewall distribuída simple. Estas ferramentas permitem-lhe detetar e bloquear o tráfego de rede indesejado.

### <a name="application-gatewayweb-application-firewall"></a>Firewall de aplicações Gateway/Web de aplicação

O [Firewall de aplicações Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) componente (WAF) do [Gateway de aplicação do Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) protege aplicações web, que são cada vez mais destinos de ataques maliciosos que explora vulnerabilidades conhecidas comuns. Uma WAF centralizada protege contra ataques da web e simplifica a gestão de segurança sem necessidade de alterações de aplicação.

Várias categorias de ataque, incluindo injeção de SQL, processamento de scripts entre sites, crawlers de violações e anomalias, bots, do protocolo HTTP, scanners, aplicação configurações incorretas comuns, HTTP Denial of Service, os endereços WAF do Azure e solicitar a outros ataques comuns, tais como a injeção de comando, HTTP smuggling, dividir de resposta HTTP e ataques de inclusão de ficheiros remoto. 

Pode criar um gateway de aplicação com WAF ou adicionar WAF para um gateway de aplicação existente. Em ambos os casos, o Gateway de aplicação do Azure requer a sua própria sub-rede.

#### <a name="how-do-i-create-an-application-gateway-with-waf"></a>Como criar um gateway de aplicação com WAF? 

Para criar um novo gateway de aplicação com WAF ativada, efetue o seguinte:

1. Inicie sessão no portal do Azure e no **Favoritos** painel do portal, clique em **novo**

2. No painel **Novo**, clique em **Redes**.

3. Clique em **Gateway de aplicação**.

4. Navegue até ao portal do Azure, **clique em novo \> redes \> Gateway de aplicação.**

   ![criar gateways de aplicação](media/protect-netsec/app-gateway-01.png)

5. No **Noções básicas** painel apresentado, introduza os valores para os seguintes campos: nome, a camada (padrão ou WAF), tamanho SKU (pequeno, médio ou grande), instância contagem (2 para elevada disponibilidade), subscrição, grupo de recursos e localização.

6. No **definições** painel que é apresentado em **rede Virtual**, clique em **escolha uma rede virtual**. Este passo é aberto o introduza painel virtual network escolha.

7. Clique em **criar nova** para abrir o **criar rede virtual** painel.

8. Introduza os seguintes valores: nome, espaço de endereços, o nome de sub-rede, o intervalo de endereços de sub-rede. Clique em **OK**.

9. No **definições** painel em **configuração de IP de front-end**, escolha o tipo de endereço IP.

10. Clique em **escolher um endereço IP público,** , em seguida, **criar novos.**

11. Aceite o valor predefinido e clique em **OK.**

12. No **definições** painel em **configuração do serviço de escuta**, selecione para utilizar HTTP ou HTTPS em **protocolo**. Para utilizar HTTPS, é necessário um certificado.

13. Configurar as definições específicas do WAF: **Firewall estado** (**ativado**) e **modo Firewall** (**prevenção**). Se optar por **deteção** como modo, o tráfego só com sessão iniciado.

14. Reveja o **resumo** página e clique em **OK**. Agora o gateway de aplicação é colocado em fila cópias de segurança e criado.

Depois de criar o gateway de aplicação, pode navegar para o mesmo no portal do e continuar a configuração do gateway de aplicação.

![gateway de aplicação criado](media/protect-netsec/adatum-app-gateway.png)

#### <a name="how-do-i-add-waf-to-an-existing-application"></a>Como posso adicionar WAF para uma aplicação existente?

Para atualizar um gateway de aplicação existente para suportar WAF no modo de prevenção, efetue o seguinte:

1. No painel **Favoritos** do portal do Azure, clique em **Todos os recursos**.

2. Clique no Gateway de aplicação existente no **todos os recursos** painel. 
>[!NOTE]
Nota: Se a subscrição que selecionou já tem vários recursos na mesma, pode introduzir o nome no filtro de por nome... para aceder facilmente à zona DNS.
3. Clique em **firewall de aplicações Web** e atualizar as definições do gateway de aplicação: **atualizar para o escalão de WAF** (selecionado), **Firewall estado** (ativado), **modo Firewall** (prevenção). Terá também de configurar o conjunto de regras e configurar regras desativadas.

Para obter informações mais detalhadas sobre como criar um novo gateway de aplicação com WAF e como adicionar WAF para um gateway de aplicação existente, consulte [criar um gateway de aplicação com firewall de aplicações web através do portal.](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)

### <a name="network-security-groups"></a>Grupos de Segurança de Rede

A [grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) contém uma lista de regras de segurança que permitem ou negam o tráfego de rede para recursos ligados a [redes virtuais do Azure](https://docs.microsoft.com/azure/virtual-network/) (VNet). Os NSGs podem ser associados a sub-redes ou VMs individuais. Quando um NSG é associado a uma sub-rede, as regras são aplicadas a todos os recursos ligados à mesma. É possível restringir ainda mais o tráfego ao associar também um NSG a uma VM ou a uma NIC.

Os NSGs contêm quatro propriedades: nome, região, grupo de recursos e as regras.
>[!Note]
Embora os NSGs existam num grupo de recursos, podem ser associados a recursos em qualquer grupo de recursos, desde que os recursos façam parte da mesma região do Azure que os NSGs.

Regras do NSG contêm nove propriedades: nome, o protocolo (TCP, UDP ou \*, que inclui o ICMP, bem como UDP e TCP), de origem de intervalo de portas, o intervalo de portas de destino, o prefixo de endereço de origem, o prefixo de endereço de destino, escreva direção (entrada ou saída), acesso e a prioridade do (entre 100 e 4096) (permitir ou negar). Todos os NSGs contêm um conjunto de regras predefinidas que pode ser eliminado ou substituído pelas regras que cria.

#### <a name="how-do-i-implement-nsgs"></a>Como implementar o NSGs?

Implementar NSGs requer planeamento e, existem várias considerações de design que terá de ter em consideração. Estes incluem os limites no número de NSGs por subscrição e regras por NSG; Conceção de VNet e sub-rede, regras especiais, tráfego ICMP, isolamento de camadas com sub-redes, balanceadores de carga e muito mais.

Para obter mais documentação de orientação planear e implementar os NSGs e um cenário de exemplo de implementação, consulte [filtrar o tráfego de rede com grupos de segurança de rede.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)

#### <a name="how-do-i-create-rules-in-an-nsg"></a>Como criar regras a num NSG?

Para criar regras de entrada num NSG existente, efetue o seguinte:

1. Clique em **procurar**e, em seguida, **grupos de segurança de rede**.

2. Na lista de NSGs, clique em **NSG-front-end**e, em seguida, **regras de segurança de entrada.**

3. Na lista de regras de segurança de entrada, clique em **adicionar.**

4. Introduza os valores nos seguintes campos: nome de prioridade, origem, protocolo, a origem de intervalo, destino, destino intervalo de portas e a ação.

A nova regra será apresentado o NSG após alguns segundos.

![regras de segurança de rede](media/protect-netsec/inbound-security.png)

Para obter mais instruções sobre como criar NSGs em sub-redes, criar regras e associar um NSG uma sub-rede do front-end e back-end, consulte [criar grupos de segurança de rede através do portal do Azure.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal)

## <a name="next-steps"></a>Passos seguintes

[Segurança de rede do Azure](https://azure.microsoft.com/blog/azure-network-security/)

[Práticas recomendadas de segurança de rede do Azure](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices)

[Obter informações sobre um grupo de segurança de rede](https://docs.microsoft.com/rest/api/network/virtualnetwork/get-information-about-a-network-security-group)

[Firewall de aplicações Web (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)

---
title: "Descrição geral de padrão de proteção do Azure DDoS | Microsoft Docs"
description: "Saiba mais sobre o serviço de proteção do Azure DDoS."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 6b26108b000bac56fe7d49a3a634f2be9d7543a8
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="azure-ddos-protection-standard-overview"></a>Descrição geral de DDoS proteção padrão do Azure

Distribuída ataques denial of service (DDoS distribuídos) são uma das preocupações de disponibilidade e segurança maiores enfrentam os clientes mover as aplicações para a nuvem. Um ataque DDoS tenta a esgotar os recursos de uma aplicação, tornando a aplicação disponível para utilizadores legítimos. Ataques de DDoS podem ser segmentados em qualquer ponto final que está publicamente acessível através da Internet.

Proteção DDoS do Azure, combinados com aplicação design as melhores práticas, fornecer defesa contra ataques de DDoS. Proteção DDos do Azure fornece os seguintes escalões de serviço: 

- **Básico de proteção de DDoS Azure**: ativada automaticamente como parte da plataforma do Azure, sem encargos adicionais. Mitigação de monitorização e em tempo real de sempre no tráfego de ataques de nível de rede comuns fornece as mesmas defesas utilizadas pelos serviços online da Microsoft. A escala completa de rede global do Azure pode ser utilizada para distribuir e mitigar o tráfego de ataque em regiões. Proteção é fornecida para IPv4 e IPv6 Azure [endereços IP públicos](virtual-network-public-ip-address.md).
- **Padrão de proteção de DDoS Azure** fornece capacidades de mitigação adicionais otimizadas especificamente para recursos de rede Virtual do Azure. É simple ativar e não necessita de aplicação alterações. Políticas de proteção são otimizadas através de monitorização do tráfego dedicado e machine learning algoritmos e aplicadas aos endereços IP públicos associados a recursos implementados em redes virtuais, por exemplo, o Balanceador de carga do Azure, o Gateway de aplicação do Azure e o Azure Instâncias de Service Fabric. Telemetria em tempo real está disponível nas vistas de monitorização do Azure durante um ataque e para o histórico. Proteções de camada de aplicação podem ser adicionadas através de [Firewall de aplicação de Web de Gateway de aplicação](https://azure.microsoft.com/services/application-gateway). Proteção é fornecida para o IPv4 Azure [endereços IP públicos](virtual-network-public-ip-address.md). 

![Padrão de proteção DDoS do Azure](./media/ddos-protection-overview/ddos-protection-overview-fig2.png)

> [!IMPORTANT]
> Padrão de proteção de DDoS do Azure está atualmente em pré-visualização. Proteção é fornecida para qualquer recurso do Azure que tem um endereço IP público do Azure associado à mesma, como máquinas virtuais, balanceadores de carga e gateways de aplicação. Terá de [registar](http://aka.ms/ddosprotection) para o serviço antes de poder ativar DDoS proteção padrão para a sua subscrição. Após o registo, o Azure DDoS equipa conacts guias e que o processo de ativação. DDoS proteção padrão está disponível na EUA leste, EUA Leste 2, EUA oeste, regiões Central EUA oeste, Europa do Norte, Europa Ocidental, oeste do Japão, leste do Japão, Leste asiático e Sudeste asiático. Durante a pré-visualização, não lhe serem cobrados para utilizar o serviço.

Aconselhamo-lo para experimentar DDoS proteção padrão, em ambientes de produção, programação ou teste. Utilize os seguintes recursos para enviar comentários suas experiências:
- [Proteção DDoS do Azure no fórum do Microsoft Azure](https://feedback.azure.com/forums/905032-azure-ddos-protection). 
- [Proteção DDoS do Azure no fórum do MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azureddosprotection)
- [Proteção Azure DDos capacidade excedida da pilha](https://stackoverflow.com/tags/azure-ddos/info)

Para problemas de suporte, pode [abrir um pedido de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md). Enquanto DDoS proteção padrão está em pré-visualização, suporte é fornecido numa base de melhor esforço.

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Tipos de ataques de DDoS atenua DDoS proteção padrão

Padrão de proteção DDoS pode mitigar estes tipos de ataques:

- **Ataques volumetric**: objetivo do ataque é inundar a camada de rede com uma quantidade substancial de tráfego seemingly legítima. Inclui inundações UDP, inundações amplificação e outras inundações pacote falsificado. Padrão de proteção DDoS atenua estes potenciais ataques de gigabytes multi absorbing e limpeza, tirar partido da escala de rede global do Azure, automaticamente. 
- **Ataques de protocolo**: estes ataques compor um destino inacessível por explorá uma vulnerabilidade na camada 3 e pilha de protocolo de camada 4. Inclui, ataques de uma inundação SIN, ataques de reflexão e outros ataques de protocolo. Padrão de proteção DDoS mitiga ataques, differentiating entre tráfego malicioso e legítimo, interagir com o cliente e a bloquear tráfego malicioso. 
- **Ataques de camada de aplicação**: estes ataques de pacotes de aplicação web para interromper a transmissão de dados entre anfitriões de destino. Inclui HTTP violações de protocolo, SQL Server injeção, processamento de scripts entre sites e outros ataques de camada 7. Utilizar o Azure [firewall de aplicações do Gateway de aplicação web](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), com DDoS proteção padrão, para fornecer defesa contra estes ataques. 

Padrão de proteção DDoS protege os recursos numa rede virtual, incluindo endereços IP públicos associados a máquinas virtuais, balanceadores de carga e gateways de aplicação. Quando conjugada com a firewall de aplicações do Gateway de aplicação web, DDoS proteção Standard pode fornecer completa camada 3 para a camada 7 capacidade de mitigação.

## <a name="ddos-protection-standard-features"></a>Funcionalidades DDoS proteção padrão

![Funcionalidade de DDoS](./media/ddos-protection-overview/ddos-overview-fig1.png)

Padrão de proteção DDoS funcionalidades incluem: 

- **Integração da plataforma nativa:** nativamente integradas no Azure e inclui a configuração através do portal do Azure e o PowerShell. Padrão de proteção DDoS compreende os recursos e a configuração do recurso.
- **A monitorização de tráfego sempre ativado:** seus padrões de tráfego de aplicação são monitorizados 24 horas por dia, 7 dias por semana, à procura de indicadores de ataques de DDoS. Mitigação é executada quando as políticas de proteção são excedidas.
- **Proteção de chave de ative:** simplificado configuração protege imediatamente todos os recursos numa rede virtual, assim que DDoS proteção padrão está ativada. Não é necessária nenhuma definição de utilizador ou intervenção. Padrão de proteção DDoS instantaneamente e automaticamente atenua o ataque, uma vez que é detetado.
- **Otimização adaptável:** a criação de perfis de tráfego inteligente aprende o tráfego da sua aplicação ao longo do tempo, seleciona e atualiza o perfil que é mais adequado para o seu serviço. O perfil ajusta como tráfego alterações ao longo do tempo.
- **A camada 3 para proteção de camada 7:** fornece proteção DDoS de pilha completa, quando utilizado com um gateway de aplicação.
- **Escala de mitigação extensas:** 60 através de tipos de ataque diferentes podem ser mitigados com capacidade global, para proteger contra os ataques de DDoS conhecidos maiores. 
- **Ataques métricas:** Summarized métricas de cada ataque estão acessíveis por meio do Monitor do Azure.
- **Alerta de ataque:** alertas podem ser configurados no início e paragem de um ataque e ao longo da duração do ataque, através de métricas de ataque incorporada. Alertas de integram o seu software operacional, como o Microsoft Operations Management Suite, Splunk, armazenamento do Azure, ao E-Mail e o portal do Azure.
- **Custo garantia:** transferência de dados e os créditos de serviço de escalamento horizontal de aplicação para ataques de DDoS documentados.

## <a name="ddos-protection-standard-mitigation"></a>Mitigação DDoS proteção padrão

Serviço de proteção DDoS da Microsoft monitoriza a utilização de tráfego real e constantemente compara-o relativamente a limiares definidos na política DDoS. Quando esse limiar de tráfego for excedido, DDoS mitigação é iniciada automaticamente. Quando o tráfego devolve abaixo do limiar, é removida a mitigação.

Durante a mitigação, é redirecionado o tráfego enviado para o recurso protegido pelo serviço de proteção DDoS e são executadas várias verificações. Estas verificações, geralmente, efetuam as seguintes funções:

- Certifique-se de pacotes está em conformidade com as especificações de Internet e não são um formato incorreto.
- Interagir com o cliente para determinar se o tráfego é, potencialmente, um pacote a denuncia (por exemplo: SIN Auth ou o Cookie de SIN ou remover um pacote para a origem de retransmiti-lo).
- Pacotes de limite de taxa, se nenhum outro método de imposição podem ser executadas.

Os blocos de proteção DDoS atacar o tráfego e reencaminhamentos restante tráfego para o destino pretendido. Dentro de alguns minutos de deteção de ataques, será notificado através de métricas de Monitor do Azure. Ao configurar o registo de telemetria DDoS proteção padrão, pode escrever os registos para as opções disponíveis para análise futura. Os dados métricos no Monitor do Azure para padrão de proteção DDoS atualmente são mantidos durante 30 dias.

Aconselhamos não clientes, para simular os seus próprios ataques de DDoS. Em vez disso, os clientes podem utilizar o canal de suporte para pedir um DDoS ataques simulação executada por redes do Azure. Um engenheiro irá contactá-lo para dispor os detalhes do ataque DDoS (portas, protocolos, destino IPs) e dispor de uma hora para agendar o teste.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a gestão utilizando o padrão de proteção DDoS [Azure PowerShell](ddos-protection-manage-ps.md) ou [portal do Azure](ddos-protection-manage-portal.md).

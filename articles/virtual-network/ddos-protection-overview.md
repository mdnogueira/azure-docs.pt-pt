---
title: "Descrição geral de padrão de proteção do Azure DDoS | Microsoft Docs"
description: "Saiba mais sobre o serviço de proteção do Azure DDoS."
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: kumud
ms.openlocfilehash: 76da0d4e805c732d40a7bd02e5c70973c792e26c
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="azure-ddos-protection-standard-overview"></a>Descrição geral de DDoS proteção padrão do Azure

>[!IMPORTANT]
>Padrão de proteção de DDoS do Azure está atualmente em pré-visualização. Um número limitado de suporte de recursos do Azure DDoS proteção padrão e num número selecione das regiões. Terá de [registar-se para o serviço](http://aka.ms/ddosprotection) durante a pré-visualização limitada para obter DDoS proteção padrão ativada para a sua subscrição. São contactados pela equipa do Azure DDoS após o registo para ajudá-lo durante o processo de ativação. DDoS proteção padrão está disponível em regiões EUA leste, EUA oeste e Central EUA oeste. Durante a pré-visualização, não lhe serem cobrados para utilizar o serviço.

Ataques de Denial of Service (DDoS) distribuídos são um da maior disponibilidade e segurança seja relativo a clientes destinado ao mover as aplicações para a nuvem. Um ataque DDoS tenta a esgotar os recursos de uma aplicação que efetua a aplicação disponível a utilizadores legítimos. Ataques de DDoS podem ser segmentados em qualquer ponto final que está publicamente acessível através da Internet.

Proteção DDoS do Azure, combinados com práticas recomendadas de conceção de aplicação fornecem defesa contra estes ataques. Estes escalões de duas serviço são fornecidos: 

- **Básico de proteção de DDoS Azure** -é automaticamente ativada como parte da plataforma do Azure, sem encargos adicionais. Mitigação de monitorização e em tempo real de sempre no tráfego de ataques de nível de rede comuns fornece as mesmas defesas utilizadas pelos serviços online da Microsoft.  A escala completa de rede global do Azure pode ser utilizada para distribuir e mitigar o tráfego de ataque em regiões. 
- **Padrão de proteção de DDoS Azure** -fornece capacidades de mitigação adicionais otimizadas especificamente para recursos de rede Virtual. É simple ativar e não necessita de aplicação alterações. Políticas de proteção são otimizadas através de monitorização do tráfego dedicado e machine learning algoritmos e aplicadas a IPs públicos associados a recursos de rede Virtual, tais como instâncias de Balanceador de carga, o Gateway de aplicação e o Service Fabric.  Telemetria de tempo real está disponível nas vistas de monitorização do Azure durante um ataque e para o histórico. Proteções de camada de aplicação podem ser adicionadas através de [Firewall de aplicação de Web de Gateway de aplicação](https://azure.microsoft.com/services/application-gateway/). 

![Padrão de proteção DDoS do Azure](./media/ddos-protection-overview/ddos-protection-overview-fig2.png)

Aconselhamo-lo para experimentar DDoS proteção padrão, em ambientes de produção, programação ou teste. Utilize os seguintes recursos para enviar comentários suas experiências:
- [Proteção DDoS do Azure no fórum do Microsoft Azure](https://feedback.azure.com/forums/905032-azure-ddos-protection). 
- [Proteção DDoS do Azure no fórum do MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azureddosprotection)
- [Proteção Azure DDos capacidade excedida da pilha](https://stackoverflow.com/tags/azure-ddos/info)

Para problemas de suporte, pode [abrir um pedido de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md). Enquanto DDoS proteção padrão está em pré-visualização, suporte é fornecido numa base de melhor esforço.

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Tipos de ataques de DDoS atenua DDoS proteção padrão

Padrão de proteção DDoS pode mitigar estes tipos de ataques:

- **Ataques volumetric** -objetivo do ataque é inundar a camada de rede com uma quantidade substancial de tráfego seemingly legítima. Inclui UDP inundações, inundações amplificação e outras inundações pacote falsificado. Padrão de proteção DDoS atenua estes potenciais ataques de gigabytes multi pelo absorbing & limpeza-los tirar automaticamente partido escala de rede global do Azure. 
- **Ataques de protocolo** -estes ataques compor um destino inacessível por explorá uma vulnerabilidade na pilha de protocolo de camada 3 e 4 de camada. Inclui, ataques de uma inundação SIN, ataques de reflexão e outros ataques de protocolo. Padrão de proteção DDoS mitiga ataques differentiating entre tráfego malicioso e legítimo por interagir com o cliente e a bloquear tráfego malicioso. 
- **Ataques de camada de aplicação** -estes ataques de pacotes de aplicação web para interromper a transmissão de dados entre anfitriões de destino. Inclui violações de protocolo HTTP, SQL Server injeção, processamento de scripts entre sites e outros ataques de camada 7. Utilizar WAF de Gateway de aplicação com o padrão de proteção DDoS para fornecer defesa contra estes ataques. 

Padrão de proteção DDoS protege os recursos numa rede virtual, incluindo os IPs públicos associados a VMs, balanceadores de carga internos e gateways de aplicação. Quando integrado no SKU de WAF do Gateway de aplicação, DDoS proteção Standard pode fornecer L3 completo para a capacidade de mitigação L7.

## <a name="ddos-protection-standard-features"></a>Funcionalidades DDoS proteção padrão

![Funcionalidade de DDoS](./media/ddos-protection-overview/ddos-overview-fig1.png)

Padrão de proteção DDoS funcionalidades incluem: 

- **Integração da plataforma nativa:** DDoS proteção padrão nativamente está integrado no Azure e inclui a configuração através do portal do Azure e o PowerShell. Padrão de proteção DDoS compreende os recursos e a configuração do recurso.
- **A monitorização de tráfego sempre ativado:** seus padrões de tráfego de aplicação são monitorizados 24x7, à procura de indicadores de ataques de DDoS. Mitigação é executada quando as políticas de proteção são excedidas.
- **Proteção de chave de ative:** simplificado configuração protege imediatamente todos os recursos numa rede virtual, assim que DDoS proteção padrão está ativada. Não é necessária nenhuma definição de utilizador ou intervenção - DDoS proteção padrão de forma instantânea e automaticamente atenua o ataque assim que foi detetado.
- **Otimização adaptável:** a criação de perfis de tráfego inteligente aprende o tráfego da sua aplicação ao longo do tempo, seleciona e atualiza o perfil que é mais adequado para o seu serviço. O perfil ajusta como tráfego alterações ao longo do tempo.
- **L3 para proteção de L7 com um gateway de aplicação:** fornecer proteção de DDoS pilha completa de funcionalidades do Gateway de aplicação WAF.
- **Escala de mitigação extensas:** 60 através de tipos de ataque diferentes podem ser mitigados com capacidade global para proteger contra os ataques de DDoS conhecidos maiores. 
- **Ataques métricas:** Summarized métricas de cada ataque estão acessíveis por meio do Monitor do Azure.
- **Alerta de ataque:** alertas podem ser configurados no início e paragem de um ataque e através de ataque da duração através de ataque incorporada métricas. Alertas de integram o seu software operacional, como o OMS, o Splunk, o Storage do Azure, o E-Mail e o portal do Azure.
- **Custo garantia:** transferência de dados e os créditos de serviço de escalamento horizontal de aplicação para ataques de DDoS documentados.

## <a name="ddos-protection-standard-mitigation"></a>Mitigação DDoS proteção padrão

Serviço de proteção DDoS da Microsoft monitoriza a utilização de tráfego real e constantemente compara-o relativamente a limiares definidos na política DDoS. Quando esse limiar de tráfego for excedido, DDoS mitigação é iniciada automaticamente. Quando o tráfego devolve abaixo do limiar, é removida a mitigação.

Durante a mitigação, é redirecionado o tráfego para o recurso protegido pelo serviço de proteção DDoS e são executadas várias verificações. Estas verificações, geralmente, execute a seguinte função:

- Certifique-se de pacotes está em conformidade com as especificações de Internet e não são um formato incorreto.
- Interagir com o cliente para determinar se é, potencialmente, um pacote a denuncia (por exemplo: SIN Auth ou o Cookie de SIN ou remover um pacote para a origem de retransmiti-lo).
- Pacotes de limite de velocidade se nenhum outro método de imposição podem ser executadas.

Os blocos de proteção DDoS atacar o tráfego e os restantes reencaminhar tráfego para o destino pretendido. Dentro de alguns minutos de deteção de ataques, será notificado através de métricas de Monitor do Azure. Ao configurar o registo de telemetria DDoS proteção padrão, pode escrever os registos para as opções disponíveis para análise futura. Os dados métricos no Monitor do Azure para padrão de proteção DDoS atualmente são mantidos durante 30 dias.

Aconselhamos não clientes, para simular os seus próprios ataques de DDoS. Em vez disso, os clientes podem utilizar o canal de suporte para pedir um DDoS ataques simulação executada por redes do Azure. Um engenheiro irá contactá-lo para dispor os detalhes do ataque DDoS (portas, protocolos, destino IPs) e dispor de uma hora para agendar o teste.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a gestão utilizando o padrão de proteção DDoS [Azure PowerShell](ddos-protection-manage-ps.md) ou [portal do Azure](ddos-protection-manage-portal.md).

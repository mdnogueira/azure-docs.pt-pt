---
title: "O Azure Site Recovery, funcionamento em rede orientações para replicar máquinas virtuais do Azure para o Azure | Microsoft Docs"
description: "Rede orientações para replicar máquinas virtuais do Azure"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/31/2017
ms.author: sujayt
ms.openlocfilehash: 99c08a1efbc610959fb4ba824dcb0601efac5877
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="networking-guidance-for-replicating-azure-virtual-machines"></a>Rede orientações para replicar máquinas virtuais do Azure

>[!NOTE]
> Replicação de recuperação de site para máquinas virtuais do Azure está atualmente em pré-visualização.

Este artigo fornece detalhes sobre a documentação de orientação de rede para o Azure Site Recovery quando estiver a replicar e recuperar máquinas virtuais do Azure a partir de uma região noutra região. Para obter mais informações sobre os requisitos do Azure Site Recovery, consulte o [pré-requisitos](site-recovery-prereq.md) artigo.

## <a name="site-recovery-architecture"></a>Arquitetura de recuperação de site

Recuperação de sites fornece uma forma simple e fácil para replicar as aplicações em execução em máquinas virtuais do Azure noutra região do Azure para que podem ser recuperados se houver uma interrupção na região primária. Saiba mais sobre [este cenário e a arquitetura da recuperação de Site](site-recovery-azure-to-azure-architecture.md).

## <a name="your-network-infrastructure"></a>A infraestrutura de rede

O diagrama seguinte ilustra o ambiente do Azure típico para uma aplicação em execução em máquinas virtuais do Azure:

![ambiente de cliente](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Se estiver a utilizar o Azure ExpressRoute ou de uma ligação de VPN a partir de uma rede no local para o Azure, o ambiente tem este aspeto:

![ambiente de cliente](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Normalmente, os clientes protegem as respetivas redes através de firewalls e/ou grupos de segurança de rede (NSGs). As firewalls podem utilizar a listas brancas baseado no URL ou baseada em IP para controlar a conectividade de rede. Os NSGs permitem regras para a utilização de intervalos de IP para controlar a conectividade de rede.

>[!IMPORTANT]
> Se estiver a utilizar um proxy autenticado para controlar a conectividade de rede, não é suportada e não é possível ativar a replicação do Site Recovery.

As secções seguintes abordam as alterações de conectividade de saída de rede que são necessárias de máquinas virtuais do Azure para replicação de recuperação de sites funcionar.

## <a name="outbound-connectivity-for-azure-site-recovery-urls"></a>Conectividade de saída para os URLs de recuperação de Site do Azure

Se estiver a utilizar qualquer proxy firewall baseada no URL para controlar a conectividade de saída, certifique-se a lista branca que estes necessários URLs do serviço Azure Site Recovery:


**URL** | **Objetivo**  
--- | ---
*.blob.core.windows.net | Necessário para que os dados podem ser escritos para a conta de armazenamento de cache na região de origem da VM.
login.microsoftonline.com | Necessário para autorização e autenticação para os URLs do serviço de recuperação de sites.
*.hypervrecoverymanager.windowsazure.com | Necessário para que a comunicação de serviço de recuperação de Site pode ocorrer a partir da VM.
*. servicebus.windows.net | Necessário para que os dados de monitorização e diagnóstico de recuperação de sites podem ser escritos a partir da VM.

## <a name="outbound-connectivity-for-azure-site-recovery-ip-ranges"></a>Conectividade de saída para intervalos de IP do Azure Site Recovery

>[!NOTE]
> Para criar automaticamente as regras do NSG necessárias no grupo de segurança de rede, pode [transferir e utilizar este script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702).

>[!IMPORTANT]
> * Recomendamos que crie as regras NSG necessárias num grupo de segurança de rede de teste e certifique-se de que existem não existem problemas antes de criar as regras num grupo de segurança de rede de produção.
> * Para criar o número necessário de regras do NSG, certifique-se de que a sua subscrição está na lista de permissões. Contacte o suporte para aumentar o limite de regras do NSG na sua subscrição.

Se estiver a utilizar qualquer proxy firewall baseada em IP ou as regras do NSG para controlar a conectividade de saída, os seguintes intervalos IP tem de estar na lista de permissões, consoante as localizações de origem e de destino das máquinas virtuais:

- Todos os intervalos IP que correspondem à localização de origem. (Pode transferir o [intervalos IP](https://www.microsoft.com/download/confirmation.aspx?id=41653).) Adicionar à lista branca é necessário para que os dados podem ser escritos para a conta de armazenamento de cache da VM.

- Todos os intervalos IP que correspondem ao Office 365 [pontos finais de endereço IP V4 de autenticação e identidade](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).

    >[!NOTE]
    > Se o novo IPs adicionadas ao intervalos de IP do Office 365 no futuro, terá de criar novas regras NSG.

- Sites de ponto final do serviço de recuperação IPs ([disponíveis num ficheiro XML](https://aka.ms/site-recovery-public-ips)), que dependem da sua localização de destino:

   **Localização de destino** | **Serviço de recuperação de sites IPs** |  **Recuperação de site do IP de monitorização**
   --- | --- | ---
   Ásia Oriental | 52.175.17.132 | 13.94.47.61
   Sudeste Asiático | 52.187.58.193 | 13.76.179.223
   Índia Central | 52.172.187.37 | 104.211.98.185
   Sul da Índia | 52.172.46.220 | 104.211.224.190
   EUA Centro-Norte | 23.96.195.247 | 168.62.249.226
   Europa do Norte | 40.69.212.238 | 52.169.18.8
   Europa Ocidental | 52.166.13.64 | 40.68.93.145
   EUA Leste | 13.82.88.226 | 104.45.147.24
   EUA Oeste | 40.83.179.48 | 104.40.26.199
   EUA Centro-Sul | 13.84.148.14 | 104.210.146.250
   EUA Central | 40.69.144.231 | 52.165.34.144
   EUA Leste 2 | 52.184.158.163 | 40.79.44.59
   Leste do Japão | 52.185.150.140 | 138.91.1.105
   Oeste do Japão | 52.175.146.69 | 138.91.17.38
   Sul do Brasil | 191.234.185.172 | 23.97.97.36
   Leste da Austrália | 104.210.113.114 | 191.239.64.144
   Sudeste da Austrália | 13.70.159.158 | 191.239.160.45
   Canadá Central | 52.228.36.192 | 40.85.226.62
   Leste do Canadá | 52.229.125.98 | 40.86.225.142
   EUA Centro-Oeste | 52.161.20.168 | 13.78.149.209
   EUA Oeste 2 | 52.183.45.166 | 13.66.228.204
   Reino Unido Oeste | 51.141.3.203 | 51.141.14.113
   Reino Unido Sul | 51.140.43.158 | 51.140.189.52
   Sul do Reino Unido 2 | 13.87.37.4| 13.87.34.139
   Norte do Reino Unido | 51.142.209.167 | 13.87.102.68
   Coreia Central | 52.231.28.253 | 52.231.32.85
   Coreia do Sul | 52.231.298.185 | 52.231.200.144

## <a name="sample-nsg-configuration"></a>Exemplo de configuração de NSG
Esta secção explica os passos para configurar as regras do NSG para que possam funcionar a replicação do Site Recovery numa máquina virtual. Se estiver a utilizar as regras do NSG para controlar a conectividade de saída, utilize "Permitir HTTPS a saída" regras para todos os intervalos IP necessários.

>[!Note]
> Para criar automaticamente as regras do NSG necessárias no grupo de segurança de rede, pode [transferir e utilizar este script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702).

Por exemplo, se a localização de origem da VM é "Leste EUA" e a localização de destino de replicação é "Central EUA", siga os passos nas dois secções.

>[!IMPORTANT]
> * Recomendamos que crie as regras NSG necessárias num grupo de segurança de rede de teste e certifique-se de que existem não existem problemas antes de criar as regras num grupo de segurança de rede de produção.
> * Para criar o número necessário de regras do NSG, certifique-se de que a sua subscrição está na lista de permissões. Contacte o suporte para aumentar o limite de regras do NSG na sua subscrição.

### <a name="nsg-rules-on-the-east-us-network-security-group"></a>Regras do NSG no grupo de segurança de rede de EUA leste

* Criar regras que correspondem aos [intervalos de IP do Leste E.U.A.](https://www.microsoft.com/download/confirmation.aspx?id=41653) Isto é necessário para que os dados podem ser escritos para a conta de armazenamento de cache da VM.

* Criar regras para todos os intervalos IP que correspondem ao Office 365 [pontos finais de endereço IP V4 de autenticação e identidade](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).

* Crie regras que correspondem a localização de destino:

   **Localização** | **Serviço de recuperação de sites IPs** |  **Recuperação de site do IP de monitorização**
    --- | --- | ---
   EUA Central | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules-on-the-central-us-network-security-group"></a>Regras do NSG no grupo de segurança de rede e.u. a Central

Estas regras são necessárias para que a replicação pode ser ativada a partir da região de destino para a origem região pós-falha:

* Regras que correspondem aos [intervalos de IP de E.U.A. Central](https://www.microsoft.com/download/confirmation.aspx?id=41653). Estes são necessários para que os dados podem ser escritos para a conta de armazenamento de cache da VM.

* Regras para todos os intervalos IP que correspondem ao Office 365 [pontos finais de endereço IP V4 de autenticação e identidade](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).

* Regras que correspondem à localização de origem:

   **Localização** | **Serviço de recuperação de sites IPs** |  **Recuperação de site do IP de monitorização**
    --- | --- | ---
   EUA Leste | 13.82.88.226 | 104.45.147.24


## <a name="guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration"></a>Diretrizes para a configuração do Azure-para-no local. o ExpressRoute/VPN existente

Se tiver uma ligação ExpressRoute ou VPN entre no local e a localização de origem no Azure, siga as orientações nesta secção.

### <a name="forced-tunneling-configuration"></a>Forçado a configuração de túnel

Uma configuração de cliente comum consiste em definir uma rota predefinida (0.0.0.0/0) que força o tráfego de Internet de saída a passagem a localização no local. Não recomendamos esta. O tráfego de replicação e comunicação de serviço de recuperação de Site não devem deixar o limite do Azure. A solução consiste em adicionar as rotas definidas pelo utilizador (UDRs) para [estes intervalos IP](#outbound-connectivity-for-azure-site-recovery-ip-ranges) para que o tráfego de replicação não é aceite no local.

### <a name="connectivity-between-the-target-and-on-premises-location"></a>Conectividade entre a localização de destino e no local

Siga estas diretrizes para ligações entre a localização de destino e a localização no local:
- Se a aplicação tem de ligar às máquinas no local ou se não existirem clientes que ligam à aplicação no local através de VPN/ExpressRoute, certifique-se de que tem, pelo menos, um [ligação site a site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) entre a sua região do Azure de destino e o Centro de dados no local.

- Se espera que uma grande quantidade de tráfego flua entre a sua região do Azure de destino e o Centro de dados no local, deve criar outro [ligação do ExpressRoute](../expressroute/expressroute-introduction.md) entre a região do Azure de destino e o Centro de dados no local.

- Se pretender manter IPs para as máquinas virtuais depois de efetuar a ativação pós-falha, mantenha a ligação de site para site/ExpressRoute a região de destino no estado desligado. Este é certificar-se de que não haja nenhum conflito de intervalo entre os intervalos IP da região de origem e intervalos IP da região de destino.

### <a name="best-practices-for-expressroute-configuration"></a>Melhores práticas para a configuração do ExpressRoute
Siga estas práticas recomendadas para a configuração do ExpressRoute:

- Terá de criar um circuito ExpressRoute em regiões de origem e de destino. Em seguida, terá de criar uma ligação entre:
  - A rede virtual de origem e o circuito ExpressRoute.
  - A rede virtual de destino e o circuito ExpressRoute.

- Como parte do ExpressRoute standard, pode criar circuitos na mesma região geopolítica. Para criar circuito do ExpressRoute em diferentes regiões geopolíticas, Azure ExpressRoute Premium é necessário, que envolve um custo incremental. (Se já estiver a utilizar o ExpressRoute Premium, existe um custo extra.) Para obter mais detalhes, consulte o [documento de localizações do ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) e [ExpressRoute preços](https://azure.microsoft.com/pricing/details/expressroute/).

- Recomendamos que utilize diferentes intervalos IP em regiões de origem e de destino. O circuito de ExpressRoute não conseguirão estabelecer ligação com duas redes virtuais do Azure, os mesmos de intervalos de IP ao mesmo tempo.

- Pode criar redes virtuais com os mesmos intervalos IP em ambas as regiões e, em seguida, criar circuito do ExpressRoute em ambas as regiões. No caso de um evento de ativação pós-falha, desligue o circuito a rede virtual de origem e ligar o circuito na rede virtual de destino.

 >[!IMPORTANT]
 > Se a região primária completamente estiver em baixo, pode efetuar a operação desligar. Que impede que a rede virtual de destino ao obter a conectividade do ExpressRoute.

## <a name="next-steps"></a>Passos seguintes
Começar a proteger as cargas de trabalho por [replicar máquinas virtuais do Azure](site-recovery-azure-to-azure.md).

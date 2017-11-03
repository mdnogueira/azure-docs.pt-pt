---
title: Ligar ao Azure Data Lake Store, a partir de VNETs | Microsoft Docs
description: Ligar ao Azure Data Lake Store, a partir de VNETs do Azure
services: data-lake-store,data-catalog
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 029939eabc7283d7591576fa1b14731a9e626a88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="access-azure-data-lake-store-from-vms-within-an-azure-vnet"></a>Acesso do Azure Data Lake Store das VMs dentro de uma VNET do Azure
O Azure Data Lake Store é um serviço de PaaS, que é executada em endereços IP de Internet públicos. Qualquer servidor que possam ligar à Internet pública, normalmente, pode ligar para os pontos finais do Azure Data Lake Store também. Por predefinição, todas as VMs que estão em VNETs do Azure podem aceder à Internet e, por conseguinte, podem aceder ao Azure Data Lake Store. No entanto, é possível configurar as VMs numa VNET por não ter acesso à Internet. Para essas VMs, acesso ao Azure Data Lake Store é restrito bem. Bloquear o acesso de Internet público para VMs nas VNETs do Azure pode ser feito utilizando qualquer um da seguinte abordagem.

* Ao configurar grupos de segurança de rede (NSG)
* Ao configurar rotas definidas pelo utilizador (UDR)
* Ao trocar rotas através do BGP (da indústria padrão protocolo de encaminhamento dinâmico) quando o ExpressRoute é utilizado bloco acesso à Internet

Neste artigo, irá aprender a ativar o acesso ao Azure Data Lake Store a partir de VMs do Azure que foram restrito para aceder aos recursos através de um dos três métodos listados acima.

## <a name="enabling-connectivity-to-azure-data-lake-store-from-vms-with-restricted-connectivity"></a>Ativar a conectividade ao Azure Data Lake Store das VMs com conectividade restrita
Para aceder ao Azure Data Lake Store dessas VMs, tem de configurá-los para o endereço IP em que a conta do Azure Data Lake Store está disponível de acesso. Pode identificar os endereços IP para as contas de Data Lake Store ao resolver os nomes DNS das suas contas (`<account>.azuredatalakestore.net`). Para este pode utilizar ferramentas como **nslookup**. Abra uma linha de comandos no seu computador e execute o seguinte comando.

    nslookup mydatastore.azuredatalakestore.net

O resultado é semelhante a. O valor contra **endereço** propriedade é o endereço IP associado à sua conta de Data Lake Store.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Ativar a conectividade de VMs restringida ao utilizar o NSG
Quando uma regra NSG é utilizada para bloquear o acesso à Internet, em seguida, pode criar outro NSG que permite o acesso ao endereço de IP do Data Lake Store. Obter mais informações sobre regras NSG estão disponíveis em [que é um grupo de segurança de rede?](../virtual-network/virtual-networks-nsg.md). Para obter instruções sobre como criar NSGs, consulte [como gerir os NSGs no portal do Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Ativar a conectividade de VMs restringida ao utilizar UDR ou ExpressRoute
Quando as rotas, UDRs ou rotas BGP-trocados, são utilizadas para bloquear o acesso à Internet, uma rota especial tem de ser configurada para que as VMs dessas sub-redes podem aceder a pontos finais do Data Lake Store. Para obter mais informações, consulte [que são rotas definidas pelo utilizador?](../virtual-network/virtual-networks-udr-overview.md). Para obter instruções sobre como criar UDRs, consulte [UDRs criar no Gestor de recursos](../virtual-network/virtual-network-create-udr-arm-ps.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Ativar a conectividade de VMs restringida ao utilizar o ExpressRoute
Quando um circuito do ExpressRoute estiver configurado, os servidores no local podem aceder ao Data Lake Store através do peering público. Obter mais detalhes sobre como configurar o ExpressRoute para o peering público está disponível em [FAQs do ExpressRoute](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Consultar também
* [Descrição geral do Azure Data Lake Store](data-lake-store-overview.md)
* [Proteger os dados armazenados no Azure Data Lake Store](data-lake-store-security-overview.md)


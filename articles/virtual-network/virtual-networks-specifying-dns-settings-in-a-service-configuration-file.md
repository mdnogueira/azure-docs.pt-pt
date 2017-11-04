---
title: "Especificar as definições de DNS num ficheiro de configuração do serviço | Microsoft Docs"
description: "especificar definições de DNS personalizadas com o ficheiro de configuração do serviço de rede virtual"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: jdial
ms.openlocfilehash: 0fba2ea06827aff29a7a092933edb8120d668b29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Especificar as definições de DNS no ficheiro de configuração de serviço
## <a name="dns-elements"></a>Elementos DNS
Um ficheiro de configuração de serviço pode conter um elemento de DnsServers com uma lista de endereços IPv4 para os servidores de sistema de nomes de domínio (DNS) que o serviço irá utilizar. As definições no ficheiro de configuração do serviço têm precedência sobre as definições no ficheiro de configuração de rede. Para obter mais informações, consulte [esquema de configuração de serviço do Azure (. cscfg ficheiro)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**Elemento NetworkConfiguration**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> O **nome** atributo no **DnsServer** elemento é utilizado apenas como um nome de referência. Não representa o nome de anfitrião para o servidor DNS. Cada **DnsServer** valor do atributo tem de ser exclusivo em toda a subscrição completa do Microsoft Azure.
> 
> 

## <a name="see-also"></a>Veja Também
[Esquema de configuração do serviço do Azure (. cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Esquema de configuração de rede Virtual do Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Configurar uma rede Virtual com ficheiros de configuração de rede](http://go.microsoft.com/fwlink/?LinkId=248094)

[Sobre as definições de rede Virtual no Portal de gestão](http://go.microsoft.com/fwlink/?LinkId=248092)


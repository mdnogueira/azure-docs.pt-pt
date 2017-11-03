---
title: "Especificar as definições de DNS num ficheiro de configuração de rede virtual | Microsoft Docs"
description: "Como alterar as definições de servidor DNS numa rede virtual utilizando um ficheiro de configuração de rede virtual no modelo de implementação clássica"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: a8905927-92ac-42b5-8c33-8e42c000692c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: jdial
ms.openlocfilehash: ec33268915a1888509834ce6a5b2bc782a12ce4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Especificar as definições de DNS num ficheiro de configuração de rede virtual
Um ficheiro de configuração de rede tem dois elementos que pode utilizar para especificar as definições do sistema de nomes de domínio (DNS): **DnsServers** e **DnsServerRef**. Pode adicionar uma lista de servidores DNS especificando os respetivos endereços IP e referenciar nomes para o **DnsServers** elemento. Em seguida, pode utilizar um **DnsServerRef** elemento para especificar que as entradas de servidor DNS do elemento DnsServers de são utilizadas para os sites de rede diferente dentro da sua rede virtual.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação clássica.

O ficheiro de configuração de rede pode conter os seguintes elementos. O título de cada elemento está ligado a uma página que fornece informações adicionais sobre as definições do valor de elemento.

> [!IMPORTANT]
> Para obter informações sobre como configurar o ficheiro de configuração de rede, consulte [configurar uma rede Virtual utilizar um ficheiro de configuração de rede](virtual-networks-using-network-configuration-file.md). Para obter informações sobre cada elemento contido no ficheiro de configuração de rede, consulte [esquema de configuração de rede Virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).
> 
> 

[Elemento de DNS](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> O **nome** atributo no **DnsServer** elemento é utilizado apenas como uma referência para o **DnsServerRef** elemento. Não representa o nome de anfitrião para o servidor DNS. Cada **DnsServer** valor do atributo tem de ser exclusivo em toda a subscrição completa do Microsoft Azure
> 
> 

[Elemento de Sites de rede virtual](http://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> Para poder especificar esta definição para o elemento de Sites de rede virtuais, este deve ser anteriormente definido no elemento de DNS. O DnsServerRef *nome* em Sites de rede Virtual elemento tem de referenciar um valor de nome especificado no elemento de DNS para o DnsServer *nome*.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* Compreender o [esquema de configuração de rede Virtual do Azure](http://go.microsoft.com/fwlink/?LinkId=248093).
* Compreender o [esquema de configuração do serviço do Azure](https://msdn.microsoft.com/library/windowsazure/ee758710).
* [Configurar uma rede virtual com ficheiros de configuração de rede](virtual-networks-using-network-configuration-file.md).


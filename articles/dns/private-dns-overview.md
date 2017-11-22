---
title: "Utilizar o DNS do Azure para domínios privados | Microsoft Docs"
description: "Descrição geral do DNS privada que aloja o serviço no Microsoft Azure."
services: dns
documentationcenter: na
author: KumudD
manager: jennoc
editor: 
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: 95cf8ab2bd34e698e12452e062687219bad49eb6
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2017
---
# <a name="using-azure-dns-for-private-domains"></a>Utilizar o DNS do Azure para domínios privados
O sistema de nomes de domínio ou DNS, é responsável por traduzir (ou resolver) um nome de serviço para o endereço IP. O DNS do Azure é um serviço de alojamento de domínios DNS, fornecer a resolução do nome através da infraestrutura do Microsoft Azure.  Para além dos domínios DNS de acesso à internet, DNS do Azure também suporta agora privados domínios DNS como uma funcionalidade de pré-visualização.  
 
O DNS do Azure fornece um serviço DNS fiável e seguro para gerir e resolver os nomes de domínio numa rede virtual sem a necessidade de adicionar uma solução DNS personalizada. Zonas DNS privados permitem-lhe utilizar os seus próprios nomes de domínio personalizado em vez dos nomes fornecidos pelo Azure atualmente disponíveis.  Utilizando nomes de domínio personalizado ajuda-o a adaptarem-se na arquitetura de rede virtual para suit melhor as necessidades da sua organização. Proporciona uma resolução de nomes para VMs dentro de uma rede virtual e entre redes virtuais. Além disso, pode configurar os nomes de zonas com uma vista de divisão horizon - permitir uma privada e uma zona DNS pública partilhar o mesmo nome.

![Descrição geral do DNS](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

## <a name="benefits"></a>Benefícios

* **Remove a necessidade de soluções DNS personalizadas.** Anteriormente, muitos clientes criar soluções personalizadas de DNS para gerir zonas DNS na sua rede virtual.  Gestão de zona DNS pode agora ser efetuada utilizando a infraestrutura nativa do Azure, que elimina o fardo de criar e gerir soluções DNS personalizadas.

* **Utilize todos os tipos de registos DNS comuns.**  O DNS do Azure suporta registos A, AAAA, CNAME, MX, NS, PTR, SOA, SRV e TXT.

* **Gestão de registo de nome de anfitrião automática.** Juntamente com os registos DNS personalizados de alojamento, o Azure mantém automaticamente os registos de nome de anfitrião para as VMs nas redes virtuais especificadas.  Isto permite-lhe otimizar os nomes de domínio que sem necessitar de utilizar para criar soluções personalizadas de DNS ou modificar a aplicação.

* **Resolução de nome de anfitrião entre redes virtuais.** Ao contrário dos nomes de anfitrião fornecidos pelo Azure, privadas zonas DNS podem ser partilhadas entre redes virtuais.  Esta capacidade simplifica a deteção de rede em vários locais e serviço cenários, como o peering de rede virtual.

* **Ferramentas familiares e experiência de utilizador.** Para reduzir a curva de aprendizagem, esta nova oferta utiliza as ferramentas de DNS do Azure já bem estabelecidas (PowerShell, modelos do Resource Manager, REST API).

* **DNS dividido horizon suporta.** O DNS do Azure permite-lhe criar zonas com o mesmo nome que resolve para respostas diferentes de dentro de uma rede virtual e a Internet pública.  Um cenário típico no DNS de divisão horizon consiste em fornecer uma versão dedicada de um serviço para utilização no interior da rede virtual.


## <a name="pricing"></a>Preços

Zonas DNS privada é gratuitamente durante a pré-visualização gerida. Durante a disponibilidade geral, esta funcionalidade irá utilizar um baseada na utilização modelo de preços semelhante para o DNS do Azure existente da oferta. 


## <a name="next-steps"></a>Passos seguintes

Saiba como [criar uma zona DNS privada](./private-dns-getstarted-powershell.md) no DNS do Azure.

Saiba mais sobre registos e zonas DNS, visitando: [DNS zonas e as regista descrição geral](dns-zones-records.md).

Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.

